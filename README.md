# -enu-sweet-corner
<?php include "config.php"; ?>
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>sweet</title>
<link rel="stylesheet" href="style.css">
</head>
<body>


   <header>
    <nav class="navbar">
            <a href="#"class="nav-logo">
        <h2 class="logo-text">🧇The Sweet Corner</h2>
    
    
           </a>
           <ul class="nav-menu">

        <li class="nav-item">
            <a href="index.php"class="nav-link">Home</a>

        </li>
        <li class="nav-item">
            <a href="aboutus.php"class="nav-link">About Us</a>

        </li>
        <li class="nav-item">
            <a href="menu.php"class="nav-link">Menu</a>

        </li>
        <li class="nav-item">
            <a href="Reviews.php"class="nav-link">Reviews</a>

        </li>
        <li class="nav-item">
            <a href="contactus.php"class="nav-link">contact us
</a>

  

         </ul>   
        
 

    </nav>
   </header> 

<h2 class="TEXT2">Our Menu</h2>

<?php
$products = [
    ["Iced Coffee", [
        ["Vanilla Iced Coffee","photo_2025-12-25_18-37-35.jpg",4],
        ["Caramel Iced Coffee","photo_2025-12-25_19-18-30.jpg",5],
        ["Mocha Iced Coffee","photo_2025-12-25_19-25-13.jpg",5],
        ["Hazelnut Iced Coffee","photo_2025-12-25_19-28-35.jpg",4],
        ["Irish Iced Coffee","photo_2025-12-25_19-30-35.jpg",6],
        ["Chocolate Iced Coffee","photo_2025-12-25_19-32-23.jpg",5]
    ]],
    ["Crêpes", [
        ["Chocolate Crêpe","photo_2025-12-25_19-42-38.jpg",6],
        ["Banana Crêpe","photo_2025-12-25_19-43-06.jpg",6],
        ["Strawberry Crêpe","photo_2025-12-25_19-41-42.jpg",6],
        ["Nutella Crêpe","photo_2025-12-25_19-42-48.jpg",7],
        ["Cheese Crêpe","photo_2025-12-25_19-42-28 (2).jpg",5],
        ["Mixed Fruit Crêpe","photo_2025-12-25_19-41-33 (2).jpg",7]
    ]],
    ["Donuts", [
        ["Chocolate Donut","photo_2025-12-25_19-57-04.jpg",3],
        ["Strawberry Donut","photo_2025-12-25_19-57-05.jpg",3],
        ["Glazed Donut","photo_2025-12-25_19-57-06.jpg",2],
        ["Blueberry Donut","photo_2025-12-25_19-57-07.jpg",3],
        ["Vanilla Donut","photo_2025-12-25_19-57-09.jpg",3],
        ["Cinnamon Donut","photo_2025-12-25_19-57-10.jpg",3]
    ]]
];

foreach($products as $category){
    echo "<h2 class='category'>{$category[0]}</h2>";
    echo "<div class='products'>";
    foreach($category[1] as $p){
        echo '<div class="product">
                <img src="images/'.$p[1].'" alt="'.$p[0].'">
                <h3>'.$p[0].'</h3>
                <p>$'.$p[2].'</p>
                <form method="POST" action="buy.php">
                    <input type="hidden" name="product" value="'.$p[0].'">
                    <input type="hidden" name="price" value="'.$p[2].'">
                    <label>Qty: <input type="number" name="qty" min="1" value="1" class="qty"></label><br>
                    <label>Loct: <input type="text" name="location" required></label><br>
                    <label>Phone: <input type="text" name="phone" required></label><br>
                    <button class="buy-btn" type="submit" name="buy">Buy</button>
                </form>
              </div>';
    }
    echo "</div>";
}
?>

<footer class="footer">
  <div class="footer-box">
    <h3>The Sweet Corner</h3>
    <ul>
      <li>Home</li>
      <li>About Us</li>
      <li>Menu</li>
      <li>Reviews</li>
      <li>Contact Us</li>
    </ul>
  </div>

  <div class="footer-box">
    <h3>Contact</h3>
    <p>Email: thesweetcorner21@gmail.com</p>
    <p>Phone: 05 xx xx xx xx</p>
    <p>Address: Rue 21 xxx, Skikda</p>
  </div>

  <div class="footer-box">
    <h3>Suivez-nous</h3>
    <ul>
      <li>Facebook</li>
      <li>Instagram</li>
    </ul>
  </div>
</footer>
 <div class="footer-bottom">
    <p><b>© 2026 My Shop. The Sweet Corner.</b></p> </div>

</body>
</html>
