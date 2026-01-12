<?php
session_start();
if(!isset($_SESSION['admin'])){
    header("Location: login.php");
    exit();
}

include "config.php";
// Count unread messages
$msgRes = mysqli_query($conn,"SELECT COUNT(*) as total FROM contacts WHERE status='Unread'");
$msgCount = mysqli_fetch_assoc($msgRes)['total'];

/* ============ ACCEPT ORDER ============ */
if(isset($_GET['accept_order'])){
    $id = $_GET['accept_order'];
    mysqli_query($conn,"UPDATE buy_orders SET status='Accepted' WHERE id=$id");
    header("Location: admin.php");
    exit();
}

/* ============ DELETE ORDER ============ */
if(isset($_GET['delete_order'])){
    $id = $_GET['delete_order'];
    mysqli_query($conn,"DELETE FROM buy_orders WHERE id=$id");
    header("Location: admin.php");
    exit();
}




// ================= ADD PRODUCT =================
if(isset($_POST['add'])){
    $name = $_POST['name'];
    $category = $_POST['category'];
    $price = $_POST['price'];

    $image = $_FILES['image']['name'];
    $tmp = $_FILES['image']['tmp_name'];
    move_uploaded_file($tmp,"images/".$image);

    mysqli_query($conn,"INSERT INTO products(name,category,price,image)
                        VALUES('$name','$category','$price','$image')");
}

// ================= DELETE PRODUCT =================
if(isset($_GET['delete'])){
    $id = $_GET['delete'];
    mysqli_query($conn,"DELETE FROM products WHERE id=$id");
}


// ================= GET PRODUCT FOR EDIT =================
$editData = null;
if(isset($_GET['edit'])){
    $id = $_GET['edit'];
    $res = mysqli_query($conn,"SELECT * FROM products WHERE id=$id");
    $editData = mysqli_fetch_assoc($res);
}

// ================= UPDATE PRODUCT =================
if(isset($_POST['update'])){
    $id = $_POST['id'];
    $name = $_POST['name'];
    $category = $_POST['category'];
    $price = $_POST['price'];

    if(!empty($_FILES['image']['name'])){
        $image = $_FILES['image']['name'];
        $tmp = $_FILES['image']['tmp_name'];
        move_uploaded_file($tmp,"images/".$image);

        mysqli_query($conn,"UPDATE products 
            SET name='$name', category='$category', price='$price', image='$image'
            WHERE id=$id");
    } else {
        mysqli_query($conn,"UPDATE products 
            SET name='$name', category='$category', price='$price'
            WHERE id=$id");
    }

    header("Location: admin.php");
    exit();
}
?>

<!DOCTYPE html>
<html>
<head>
<title>sweet</title>
<style>

body{
    font-family:Arial;
    background:#f8f8f8;
    padding:20px;
}
form,table{
    background:white;
    padding:15px;
    border-radius:8px;
}
input,select,button{
    width:100%;
    padding:8px;
    margin:5px 0;
}
table{
    width:100%;
    margin-top:20px;
}
th,td{
    padding:10px;
    text-align:center;
}
img{
    border-radius:5px;
}
a{
    text-decoration:none;
    margin:0 5px;
}
.logout{
    float:right;
}
.msg-icon{
    position:fixed;
    right:20px;
    bottom:20px;
    background:#0084ff;
    color:white;
    width:55px;
    height:55px;
    border-radius:50%;
    display:flex;
    justify-content:center;
    align-items:center;
    font-size:26px;
    text-decoration:none;
}

.badge{
    position:absolute;
    top:5px;
    right:5px;
    background:red;
    color:white;
    font-size:12px;
    padding:3px 7px;
    border-radius:50%;
}
</style>
</head>
<body>
    <a href="admin_messages.php" class="msg-icon">
    💬
    <?php if($msgCount > 0): ?>
        <span class="badge"><?= $msgCount ?></span>
    <?php endif; ?>
</a>

<h2>🧇 The Sweet Corner – Admin Panel
<a class="logout" href="logout.php"> Logout</a>
</h2>


<form method="POST" enctype="multipart/form-data">
    <input type="hidden" name="id" value="<?= $editData['id'] ?? '' ?>">

    <input type="text" name="name" placeholder="Product Name"
           value="<?= $editData['name'] ?? '' ?>" required>

    <select name="category">
        <?php
        $cats = ["Iced Coffee","Crêpes","Donuts"];
        foreach($cats as $cat){
            $selected = (isset($editData) && $editData['category']==$cat) ? "selected" : "";
            echo "<option $selected>$cat</option>";
        }
        ?>
    </select>

    <input type="number" step="0.01" name="price" placeholder="Price"
           value="<?= $editData['price'] ?? '' ?>" required>

    <input type="file" name="image">

    <?php if($editData): ?>
        <button name="update">Update Product</button>
        <a href="admin.php">Cancel</a>
    <?php else: ?>
        <button name="add">Add Product</button>
    <?php endif; ?>
</form>

<!-- ============ PRODUCTS TABLE ============ -->
<table border="1">
<tr>
    <th>ID</th>
    <th>Name</th>
    <th>Category</th>
    <th>Price</th>
    <th>Image</th>
    <th>Actions</th>
</tr>

<?php
$res = mysqli_query($conn,"SELECT * FROM products");
while($row = mysqli_fetch_assoc($res)){
?>
<tr>
    <td><?= $row['id'] ?></td>
    <td><?= $row['name'] ?></td>
    <td><?= $row['category'] ?></td>
    <td><?= $row['price'] ?> DA</td>
    <td><img src="images/<?= $row['image'] ?>" width="60"></td>
    <td>
        <a href="admin.php?edit=<?= $row['id'] ?>"> Edit</a>
        <a style="color:red"
           href="admin.php?delete=<?= $row['id'] ?>"
           onclick="return confirm('Delete this product?')">🗑 Delete</a>
    </td>
</tr>
<?php } ?>

</table>
<h2 style="margin-top:40px;">🛒 Orders From Menu</h2>

<table border="1" width="100%" cellpadding="10">
<tr>
    <th>ID</th>
    <th>Product</th>
    <th>Price</th>
    <th>Quantity</th>
    <th>Total</th>
    <th>Location</th>
    <th>Phone</th>
    <th>Status</th>
    <th>Date</th>
    <th>Actions</th>
</tr>

<?php
$res = mysqli_query($conn,"SELECT * FROM buy_orders ORDER BY created_at DESC");
while($o = mysqli_fetch_assoc($res)){
?>
<tr>
    <td><?= $o['id'] ?></td>
    <td><?= $o['product_name'] ?></td>
    <td><?= $o['price'] ?> DA</td>
    <td><?= $o['quantity'] ?></td>
    <td><?= $o['total'] ?> DA</td>
    <td><?= $o['location'] ?></td>
    <td><?= $o['phone'] ?></td>

    <td>
        <?php if($o['status']=="Pending"): ?>
            ⏳ Pending
        <?php else: ?>
            ✅ Accepted
        <?php endif; ?>
    </td>

    <td><?= $o['created_at'] ?></td>

    <td>
        <?php if($o['status']=="Pending"): ?>
            <a href="admin.php?accept_order=<?= $o['id'] ?>">✅ Accept</a><br>
        <?php endif; ?>

        <a style="color:red"
           href="admin.php?delete_order=<?= $o['id'] ?>"
           onclick="return confirm('Delete this order?')">
           🗑 Delete
        </a>
    </td>
</tr>
<?php } ?>
</table>

</body>
</html>
