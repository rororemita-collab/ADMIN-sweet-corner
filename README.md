<?php
session_start();
if(!isset($_SESSION['admin'])){
    header("Location: login.php");
    exit();
}

include "config.php";

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
</style>
</head>
<body>

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

</body>
</html>
