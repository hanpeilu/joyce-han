admin.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();


if (($_POST['username']) && ($_POST['passwd'])) {
  // they have just tried logging in

    $username = $_POST['username'];
    $passwd = $_POST['passwd'];

    if (login($username, $passwd)) {
      // if they are in the database register the user id
      $_SESSION['admin_user'] = $username;

   } else {
      // unsuccessful login
      do_html_header("Problem:");
      echo "<p>You could not be logged in.<br/>
            You must be logged in to view this page.</p>";
      do_html_url('login.php', 'Login');
      do_html_footer();
      exit;
    }
}

do_html_header("Administration");
if (check_admin_user()) {
  display_admin_menu();
} else {
  echo "<p>You are not authorized to enter the administration area.</p>";
}
do_html_footer();

?>

admin_fns.php
<?php
// This file contains functions used by the admin interface
// for the Book-O-Rama shopping cart.

function display_category_form($category = '') {
// This displays the category form.
// This form can be used for inserting or editing categories.
// To insert, don't pass any parameters.  This will set $edit
// to false, and the form will go to insert_category.php.
// To update, pass an array containing a category.  The
// form will contain the old data and point to update_category.php.
// It will also add a "Delete category" button.

  // if passed an existing category, proceed in "edit mode"
  $edit = is_array($category);

  // most of the form is in plain HTML with some
  // optional PHP bits throughout
?>
  <form method="post"
      action="<?php echo $edit ? 'edit_category.php' : 'insert_category.php'; ?>">
  <table border="0">
  <tr>
    <td>Category Name:</td>
    <td><input type="text" name="catname" size="40" maxlength="40"
          value="<?php echo $edit ? $category['catname'] : ''; ?>" /></td>
   </tr>
  <tr>
    <td <?php if (!$edit) { echo "colspan=2";} ?> align="center">
      <?php
         if ($edit) {
            echo "<input type=\"hidden\" name=\"catid\" value=\"".$category['catid']."\" />";
         }
      ?>
      <input type="submit"
       value="<?php echo $edit ? 'Update' : 'Add'; ?> Category" /></form>
     </td>
     <?php
        if ($edit) {
          //allow deletion of existing categories
          echo "<td>
                <form method=\"post\" action=\"delete_category.php\">
                <input type=\"hidden\" name=\"catid\" value=\"".$category['catid']."\" />
                <input type=\"submit\" value=\"Delete category\" />
                </form></td>";
       }
     ?>
  </tr>
  </table>
<?php
}

function display_book_form($book = '') {
// This displays the book form.
// It is very similar to the category form.
// This form can be used for inserting or editing books.
// To insert, don't pass any parameters.  This will set $edit
// to false, and the form will go to insert_book.php.
// To update, pass an array containing a book.  The
// form will be displayed with the old data and point to update_book.php.
// It will also add a "Delete book" button.


  // if passed an existing book, proceed in "edit mode"
  $edit = is_array($book);

  // most of the form is in plain HTML with some
  // optional PHP bits throughout
?>
  <form method="post"
        action="<?php echo $edit ? 'edit_book.php' : 'insert_book.php';?>">
  <table border="0">
  <tr>
    <td>job number:</td>
    <td><input type="text" name="isbn"
         value="<?php echo $edit ? $book['isbn'] : ''; ?>" /></td>
  </tr>
  <tr>
    <td>job name:</td>
    <td><input type="text" name="title"
         value="<?php echo $edit ? $book['title'] : ''; ?>" /></td>
  </tr>
  <tr>
    <td>job release:</td>
    <td><input type="text" name="author"
         value="<?php echo $edit ? $book['author'] : ''; ?>" /></td>
   </tr>
   <tr>
      <td>Category:</td>
      <td><select name="catid">
      <?php
          // list of possible categories comes from database
          $cat_array=get_categories();
          foreach ($cat_array as $thiscat) {
               echo "<option value=\"".$thiscat['catid']."\"";
               // if existing book, put in current catgory
               if (($edit) && ($thiscat['catid'] == $book['catid'])) {
                   echo " selected";
               }
               echo ">".$thiscat['catname']."</option>";
          }
          ?>
          </select>
        </td>
   </tr>
   <tr>
    <td>Price/day:</td>
    <td><input type="text" name="price"
               value="<?php echo $edit ? $book['price'] : ''; ?>" /></td>
   </tr>
   <tr>
     <td>Description:</td>
     <td><textarea rows="3" cols="50"
          name="description"><?php echo $edit ? $book['description'] : ''; ?></textarea></td>
    </tr>
    <tr>
      <td <?php if (!$edit) { echo "colspan=2"; }?> align="center">
         <?php
            if ($edit)
             // we need the old isbn to find book in database
             // if the isbn is being updated
             echo "<input type=\"hidden\" name=\"oldisbn\"
                    value=\"".$book['isbn']."\" />";
         ?>
        <input type="submit"
               value="<?php echo $edit ? 'Update' : 'Add'; ?> Job" />
        </form></td>
        <?php
           if ($edit) {
             echo "<td>
                   <form method=\"post\" action=\"delete_book.php\">
                   <input type=\"hidden\" name=\"isbn\"
                    value=\"".$book['isbn']."\" />
                   <input type=\"submit\" value=\"Delete job\"/>
                   </form></td>";
            }
          ?>
         </td>
      </tr>
  </table>
  </form>
<?php
}

function display_password_form() {
// displays html change password form
?>
   <br />
   <form action="change_password.php" method="post">
   <table width="250" cellpadding="2" cellspacing="0" bgcolor="#cccccc">
   <tr><td>Old password:</td>
       <td><input type="password" name="old_passwd" size="16" maxlength="16" /></td>
   </tr>
   <tr><td>New password:</td>
       <td><input type="password" name="new_passwd" size="16" maxlength="16" /></td>
   </tr>
   <tr><td>Repeat new password:</td>
       <td><input type="password" name="new_passwd2" size="16" maxlength="16" /></td>
   </tr>
   <tr><td colspan=2 align="center"><input type="submit" value="Change password">
   </td></tr>
   </table>
   <br />
<?php
}

function insert_category($catname) {
// inserts a new category into the database

   $conn = db_connect();

   // check category does not already exist
   $query = "select *
             from categories
             where catname='".$catname."'";
   $result = $conn->query($query);
   if ((!$result) || ($result->num_rows!=0)) {
     return false;
   }

   // insert new category
   $query = "insert into categories values
            ('', '".$catname."')";
   $result = $conn->query($query);
   if (!$result) {
     return false;
   } else {
     return true;
   }
}

function insert_book($isbn, $title, $author, $catid, $price, $description) {
// insert a new book into the database

   $conn = db_connect();

   // check book does not already exist
   $query = "select *
             from books
             where isbn='".$isbn."'";

   $result = $conn->query($query);
   if ((!$result) || ($result->num_rows!=0)) {
     return false;
   }

   // insert new book
   $query = "insert into books values
            ('".$isbn."', '".$author."', '".$title."',
             '".$catid."', '".$price."', '".$description."')";

   $result = $conn->query($query);
   if (!$result) {
     return false;
   } else {
     return true;
   }
}

function update_category($catid, $catname) {
// change the name of category with catid in the database

   $conn = db_connect();

   $query = "update categories
             set catname='".$catname."'
             where catid='".$catid."'";
   $result = @$conn->query($query);
   if (!$result) {
     return false;
   } else {
     return true;
   }
}

function update_book($oldisbn, $isbn, $title, $author, $catid,
                     $price, $description) {
// change details of book stored under $oldisbn in
// the database to new details in arguments

   $conn = db_connect();

   $query = "update books
             set isbn= '".$isbn."',
             title = '".$title."',
             author = '".$author."',
             catid = '".$catid."',
             price = '".$price."',
             description = '".$description."'
             where isbn = '".$oldisbn."'";

   $result = @$conn->query($query);
   if (!$result) {
     return false;
   } else {
     return true;
   }
}

function delete_category($catid) {
// Remove the category identified by catid from the db
// If there are books in the category, it will not
// be removed and the function will return false.

   $conn = db_connect();

   // check if there are any books in category
   // to avoid deletion anomalies
   $query = "select *
             from books
             where catid='".$catid."'";

   $result = @$conn->query($query);
   if ((!$result) || (@$result->num_rows > 0)) {
     return false;
   }

   $query = "delete from categories
             where catid='".$catid."'";
   $result = @$conn->query($query);
   if (!$result) {
     return false;
   } else {
     return true;
   }
}


function delete_book($isbn) {
// Deletes the book identified by $isbn from the database.

   $conn = db_connect();

   $query = "delete from books
             where isbn='".$isbn."'";
   $result = @$conn->query($query);
   if (!$result) {
     return false;
   } else {
     return true;
   }
}

?>

book_fns.php
<?php
function calculate_shipping_cost() {
  // as we are shipping products all over the world
  // via teleportation, shipping is fixed
  return 20.00;
}

function get_categories() {
   // query database for a list of categories
   $conn = db_connect();
   $query = "select catid, catname from categories";
   $result = @$conn->query($query);
   if (!$result) {
     return false;
   }
   $num_cats = @$result->num_rows;
   if ($num_cats == 0) {
      return false;
   }
   $result = db_result_to_array($result);
   return $result;
}

function get_category_name($catid) {
   // query database for the name for a category id
   $conn = db_connect();
   $query = "select catname from categories
             where catid = '".$catid."'";
   $result = @$conn->query($query);
   if (!$result) {
     return false;
   }
   $num_cats = @$result->num_rows;
   if ($num_cats == 0) {
      return false;
   }
   $row = $result->fetch_object();
   return $row->catname;
}


function get_books($catid) {
   // query database for the books in a category
   if ((!$catid) || ($catid == '')) {
     return false;
   }

   $conn = db_connect();
   $query = "select * from books where catid = '".$catid."'";
   $result = @$conn->query($query);
   if (!$result) {
     return false;
   }
   $num_books = @$result->num_rows;
   if ($num_books == 0) {
      return false;
   }
   $result = db_result_to_array($result);
   return $result;
}

function get_book_details($isbn) {
  // query database for all details for a particular book
  if ((!$isbn) || ($isbn=='')) {
     return false;
  }
  $conn = db_connect();
  $query = "select * from books where isbn='".$isbn."'";
  $result = @$conn->query($query);
  if (!$result) {
     return false;
  }
  $result = @$result->fetch_assoc();
  return $result;
}

function calculate_price($cart) {
  // sum total price for all items in shopping cart
  $price = 0.0;
  if(is_array($cart)) {
    $conn = db_connect();
    foreach($cart as $isbn => $qty) {
      $query = "select price from books where isbn='".$isbn."'";
      $result = $conn->query($query);
      if ($result) {
        $item = $result->fetch_object();
        $item_price = $item->price;
        $price +=$item_price*$qty;
      }
    }
  }
  return $price;
}

function calculate_items($cart) {
  // sum total items in shopping cart
  $items = 0;
  if(is_array($cart))   {
    foreach($cart as $isbn => $qty) {
      $items += $qty;
    }
  }
  return $items;
}
?>

book_sc.sql
create database book_sc;

use book_sc;

create table customers
(
  customerid int unsigned not null auto_increment primary key,
  name char(60) not null,
  address char(80) not null,
  city char(30) not null,
  state char(20),
  zip char(10),
  country char(20) not null
);

create table orders
(
  orderid int unsigned not null auto_increment primary key,
  customerid int unsigned not null,
  amount float(6,2),
  date date not null,
  order_status char(10),
  ship_name char(60) not null,
  ship_address char(80) not null,
  ship_city char(30) not null,
  ship_state char(20),
  ship_zip char(10),
  ship_country char(20) not null
);

create table books
(
   isbn char(13) not null primary key,
   author char(80),
   title char(100),
   catid int unsigned,
   price float(4,2) not null,
   description varchar(255)
);

create table categories
(
  catid int unsigned not null auto_increment primary key,
  catname char(60) not null
);

create table order_items
(
  orderid int unsigned not null,
  isbn char(13) not null,
  item_price float(4,2) not null,
  quantity tinyint unsigned not null,
  primary key (orderid, isbn)
);

create table admin
(
  username char(16) not null primary key,
  password char(40) not null
);

grant select, insert, update, delete
on book_sc.*
to book_sc@localhost identified by 'password';

book_sc_fns.php
<?php
  include_once('db_fns.php');
  include_once('data_valid_fns.php');
  include_once('output_fns.php');
  include_once('book_fns.php');
  include_once('user_auth_fns.php');
  include_once('admin_fns.php');
  include_once('order_fns.php');
?>

change_password.php
<?php
 require_once('book_sc_fns.php');
 session_start();
 do_html_header('Changing password');
 check_admin_user();
 if (!filled_out($_POST)) {
   echo "<p>You have not filled out the form completely.<br/>
         Please try again.</p>";
   do_html_url("admin.php", "Back to administration menu");
   do_html_footer();
   exit;
 } else {
   $new_passwd = $_POST['new_passwd'];
   $new_passwd2 = $_POST['new_passwd2'];
   $old_passwd = $_POST['old_passwd'];
   if ($new_passwd != $new_passwd2) {
      echo "<p>Passwords entered were not the same.  Not changed.</p>";
   } else if ((strlen($new_passwd)>16) || (strlen($new_passwd)<6)) {
      echo "<p>New password must be between 6 and 16 characters.  Try again.</p>";
   } else {
      // attempt update
      if (change_password($_SESSION['admin_user'], $old_passwd, $new_passwd)) {
         echo "<p>Password changed.</p>";
      } else {
         echo "<p>Password could not be changed.</p>";
      }
   }
 }
 do_html_url("admin.php", "Back to administration menu");
 do_html_footer();
?>

change_password_form.php
<?php
 require_once('book_sc_fns.php');
 session_start();
 do_html_header("Change administrator password");
 check_admin_user();

 display_password_form();

 do_html_url("admin.php", "Back to administration menu");
 do_html_footer();
?>

checkout.php
<?php
  //include our function set
  include ('book_sc_fns.php');

  // The shopping cart needs sessions, so start one
  session_start();

  do_html_header("Checkout");

  if(($_SESSION['cart']) && (array_count_values($_SESSION['cart']))) {
    display_cart($_SESSION['cart'], false, 0);
    display_checkout_form();
  } else {
    echo "<p>There are no items in your cart</p>";
  }

  display_button("show_cart.php", "continue-shopping", "Continue Shopping");

  do_html_footer();
?>

data_valid_fns.php
<?php

function filled_out($form_vars) {
  // test that each variable has a value
  foreach ($form_vars as $key => $value) {
     if ((!isset($key)) || ($value == '')) {
        return false;
     }
  }
  return true;
}

function valid_email($address) {
  // check an email address is possibly valid
  if (ereg("^[a-zA-Z0-9_\.\-]+@[a-zA-Z0-9\-]+\.[a-zA-Z0-9\-\.]+$", $address)) {
    return true;
  } else {
    return false;
  }
}

?>

db_fns.php
<?php

function db_connect() {
   $result = new mysqli('localhost', 'cfs', 'chenfushan', 'book_sc');
   if (!$result) {
      return false;
   }
   $result->autocommit(TRUE);
   return $result;
}

function db_result_to_array($result) {
   $res_array = array();

   for ($count=0; $row = $result->fetch_assoc(); $count++) {
     $res_array[$count] = $row;
   }

   return $res_array;
}

?>

delete_book.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Deleting book");
if (check_admin_user()) {
  if (isset($_POST['isbn'])) {
    $isbn = $_POST['isbn'];
    if(delete_book($isbn)) {
      echo "<p>Book ".$isbn." was deleted.</p>";
    } else {
      echo "<p>Book ".$isbn." could not be deleted.</p>";
    }
  } else {
    echo "<p>We need an ISBN to delete a book.  Please try again.</p>";
  }
  do_html_url("admin.php", "Back to administration menu");
} else {
  echo "<p>You are not authorised to view this page.</p>";
}

do_html_footer();

?>

delete_category.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Deleting category");
if (check_admin_user()) {
  if (isset($_POST['catid'])) {
    if(delete_category($_POST['catid'])) {
      echo "<p>Category was deleted.</p>";
    } else {
      echo "<p>Category could not be deleted.<br />
            This is usually because it is not empty.</p>";
    }
  } else {
    echo "<p>No category specified.  Please try again.</p>";
  }
  do_html_url("admin.php", "Back to administration menu");
} else {
  echo "<p>You are not authorised to view this page.</p>";
}
do_html_footer();

?>

edit_book.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Updating book");
if (check_admin_user()) {
  if (filled_out($_POST)) {
    $oldisbn = $_POST['oldisbn'];
    $isbn = $_POST['isbn'];
    $title = $_POST['title'];
    $author = $_POST['author'];
    $catid = $_POST['catid'];
    $price = $_POST['price'];
    $description = $_POST['description'];

    if(update_book($oldisbn, $isbn, $title, $author, $catid, $price, $description)) {
      echo "<p>Book was updated.</p>";
    } else {
      echo "<p>Book could not be updated.</p>";
    }
  } else {
    echo "<p>You have not filled out the form.  Please try again.</p>";
  }
  do_html_url("admin.php", "Back to administration menu");
} else {
  echo "<p>You are not authorised to view this page.</p>";
}

do_html_footer();

?>

edit_book_form.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Edit book details");
if (check_admin_user()) {
  if ($book = get_book_details($_GET['isbn'])) {
    display_book_form($book);
  } else {
    echo "<p>Could not retrieve book details.</p>";
  }
  do_html_url("admin.php", "Back to administration menu");
} else {
  echo "<p>You are not authorized to enter the administration area.</p>";
}
do_html_footer();

?>

edit_category.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Updating category");
if (check_admin_user()) {
  if (filled_out($_POST)) {
    if(update_category($_POST['catid'], $_POST['catname'])) {
      echo "<p>Category was updated.</p>";
    } else {
      echo "<p>Category could not be updated.</p>";
    }
  } else {
    echo "<p>You have not filled out the form.  Please try again.</p>";
  }
  do_html_url("admin.php", "Back to administration menu");
} else {
  echo "<p>You are not authorised to view this page.</p>";
}
do_html_footer();

?>

edit_category_form.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Edit category");
if (check_admin_user()) {
  if ($catname = get_category_name($_GET['catid'])) {
    $catid = $_GET['catid'];
    $cat = compact('catname', 'catid');
    display_category_form($cat);
  } else {
    echo "<p>Could not retrieve category details.</p>";
  }
  do_html_url("admin.php", "Back to administration menu");
} else {
  echo "<p>You are not authorized to enter the administration area.</p>";
}
do_html_footer();

?>

index.php
<?php
  include ('book_sc_fns.php');
  // The shopping cart needs sessions, so start one
  session_start();
  do_html_header("Welcome to Part-Time Web Site");

  echo "<p>Please choose a category:</p>";

  // get categories out of database
  $cat_array = get_categories();

  // display as links to cat pages
  display_categories($cat_array);

  // if logged in as admin, show add, delete, edit cat links
  if(isset($_SESSION['admin_user'])) {
    display_button("admin.php", "admin-menu", "Admin Menu");
  }
  do_html_footer();
?>

insert_book.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Adding a book");
if (check_admin_user()) {
  if (filled_out($_POST)) {
    $isbn = $_POST['isbn'];
    $title = $_POST['title'];
    $author = $_POST['author'];
    $catid = $_POST['catid'];
    $price = $_POST['price'];
    $description = $_POST['description'];

    if(insert_book($isbn, $title, $author, $catid, $price, $description)) {
      echo "<p>Book <em>".stripslashes($title)."</em> was added to the database.</p>";
    } else {
      echo "<p>Book <em>".stripslashes($title)."</em> could not be added to the database.</p>";
    }
  } else {
    echo "<p>You have not filled out the form.  Please try again.</p>";
  }

  do_html_url("admin.php", "Back to administration menu");
} else {
  echo "<p>You are not authorised to view this page.</p>";
}

do_html_footer();

?>

insert_book_form.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Add a part-time job");
if (check_admin_user()) {
  display_book_form();
  do_html_url("admin.php", "Back to administration menu");
} else {
  echo "<p>You are not authorized to enter the administration area.</p>";
}
do_html_footer();

?>

insert_category.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Adding a category");
if (check_admin_user()) {
  if (filled_out($_POST))   {
    $catname = $_POST['catname'];
    if(insert_category($catname)) {
      echo "<p>Category \"".$catname."\" was added to the database.</p>";
    } else {
      echo "<p>Category \"".$catname."\" could not be added to the database.</p>";
    }
  } else {
    echo "<p>You have not filled out the form.  Please try again.</p>";
  }
  do_html_url('admin.php', 'Back to administration menu');
} else {
  echo "<p>You are not authorised to view this page.</p>";
}

do_html_footer();

?>

insert_category_form.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();

do_html_header("Add a category");
if (check_admin_user()) {
  display_category_form();
  do_html_url("admin.php", "Back to administration menu");
} else {
  echo "<p>You are not authorized to enter the administration area.</p>";
}
do_html_footer();

?>

jianzhi.sql
-- MySQL dump 10.13  Distrib 5.1.41, for Win32 (ia32)
--
-- Host: localhost    Database: book_sc
-- ------------------------------------------------------
-- Server version	5.1.41

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `admin`
--

DROP TABLE IF EXISTS `admin`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `admin` (
  `username` char(16) NOT NULL,
  `password` char(40) NOT NULL,
  PRIMARY KEY (`username`)
) ENGINE=MyISAM DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `admin`
--

LOCK TABLES `admin` WRITE;
/*!40000 ALTER TABLE `admin` DISABLE KEYS */;
INSERT INTO `admin` VALUES ('admin','1de6bde166c77a2b003130b63fea7241e610b11f'),('chen','1de6bde166c77a2b003130b63fea7241e610b11f');
/*!40000 ALTER TABLE `admin` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `books`
--

DROP TABLE IF EXISTS `books`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `books` (
  `isbn` char(13) NOT NULL,
  `author` char(100) DEFAULT NULL,
  `title` char(100) DEFAULT NULL,
  `catid` int(10) unsigned DEFAULT NULL,
  `price` float(6,2) NOT NULL,
  `description` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`isbn`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `books`
--

LOCK TABLES `books` WRITE;
/*!40000 ALTER TABLE `books` DISABLE KEYS */;
INSERT INTO `books` VALUES ('0672319241','Sterling Hughes and Andrei Zmievski','PHP Developer\'s Cookbook',1,39.99,'Provides a complete, solutions-oriented guide to the challenges most often faced by PHP developers\r\nWritten specifically for experienced Web developers, the book offers real-world solutions to real-world needs\r\n'),('0672329166','Luke Welling and Laura Thomson','PHP and MySQL Web Development',1,49.99,'PHP & MySQL Web Development teaches the reader to develop dynamic, secure e-commerce web sites. You will learn to integrate and implement these technologies by following real-world examples and working sample projects.'),('067232976X','Julie Meloni','Sams Teach Yourself PHP, MySQL and Apache All-in-One',1,34.99,'Using a straightforward, step-by-step approach, each lesson in this book builds on the previous ones, enabling you to learn the essentials of PHP scripting, MySQL databases, and the Apache web server from the ground up.'),('1','XDU','family education',6,120.00,'Parents hired a tutor for the children not only consider their own aspirations; teachers should be found between child and family psychology and learning the best starting point, let the children accepted a voluntary state of knowledge, enjoy tutoring bri'),('2','XDU','Student volunteers',6,100.00,'1, the object: college students, community youth, had participated in the Olympic Games, World Expo volunteer work or have served as artistic skills of students in priority.\r\n2, condition: dedicated, loving, responsible, dedicated, well-behaved, aged 18-3'),('3','myself','Part-time summer',6,110.00,'Requirements: girls cheerful, active, shapely, face pale and fair, strong affinity\r\nWorking Hours: Saturday and Sunday, summer job, working eight hours a day\r\nWorking salary of 120 yuan / day many stores, the nearest distribution, requires a lot of staff.');
/*!40000 ALTER TABLE `books` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `categories`
--

DROP TABLE IF EXISTS `categories`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `categories` (
  `catid` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `catname` char(60) NOT NULL,
  PRIMARY KEY (`catid`)
) ENGINE=InnoDB AUTO_INCREMENT=10 DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `categories`
--

LOCK TABLES `categories` WRITE;
/*!40000 ALTER TABLE `categories` DISABLE KEYS */;
INSERT INTO `categories` VALUES (6,'Student-part-time-job'),(7,'Photographer'),(8,'Miss Manners'),(9,'Promoters');
/*!40000 ALTER TABLE `categories` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `customers`
--

DROP TABLE IF EXISTS `customers`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `customers` (
  `customerid` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` char(60) NOT NULL,
  `address` char(80) NOT NULL,
  `city` char(30) NOT NULL,
  `state` char(20) DEFAULT NULL,
  `zip` char(10) DEFAULT NULL,
  `country` char(20) NOT NULL,
  PRIMARY KEY (`customerid`)
) ENGINE=InnoDB AUTO_INCREMENT=10 DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `customers`
--

LOCK TABLES `customers` WRITE;
/*!40000 ALTER TABLE `customers` DISABLE KEYS */;
INSERT INTO `customers` VALUES (9,'chen','脦梅掳虏碌莽脳脫驴脝录录麓贸脩搂鲁陇掳虏脨拢脟酶','潞拢碌铆脟酶','卤卤戮漏','710126','脰脨鹿煤');
/*!40000 ALTER TABLE `customers` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `order_items`
--

DROP TABLE IF EXISTS `order_items`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `order_items` (
  `orderid` int(10) unsigned NOT NULL,
  `isbn` char(13) NOT NULL,
  `item_price` float(6,2) NOT NULL,
  `quantity` tinyint(3) unsigned NOT NULL,
  PRIMARY KEY (`orderid`,`isbn`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `order_items`
--

LOCK TABLES `order_items` WRITE;
/*!40000 ALTER TABLE `order_items` DISABLE KEYS */;
INSERT INTO `order_items` VALUES (9,'0672319241',39.99,1),(13,'0672319241',39.99,1);
/*!40000 ALTER TABLE `order_items` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `orders`
--

DROP TABLE IF EXISTS `orders`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `orders` (
  `orderid` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `customerid` int(10) unsigned NOT NULL,
  `amount` float(6,2) DEFAULT NULL,
  `today_date` date NOT NULL,
  `order_status` char(10) DEFAULT NULL,
  `ship_name` char(60) NOT NULL,
  `ship_address` char(80) NOT NULL,
  `ship_city` char(30) NOT NULL,
  `ship_state` char(20) DEFAULT NULL,
  `ship_zip` char(10) DEFAULT NULL,
  `ship_country` char(20) NOT NULL,
  PRIMARY KEY (`orderid`)
) ENGINE=InnoDB AUTO_INCREMENT=16 DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `orders`
--

LOCK TABLES `orders` WRITE;
/*!40000 ALTER TABLE `orders` DISABLE KEYS */;
INSERT INTO `orders` VALUES (9,9,39.99,'2013-02-13','PARTIAL','chen','脦梅掳虏碌莽脳脫驴脝录录麓贸脩搂鲁陇掳虏脨拢脟酶','潞拢碌铆脟酶','卤卤戮漏','710126','脰脨鹿煤'),(13,0,39.99,'2013-02-14','PARTIAL','chen','脦梅掳虏碌莽脳脫驴脝录录麓贸脩搂鲁陇掳虏脨拢脟酶','潞拢碌铆脟酶','卤卤戮漏','710126','脰脨鹿煤'),(14,0,39.99,'2013-02-14','PARTIAL','chen','脦梅掳虏碌莽脳脫驴脝录录麓贸脩搂鲁陇掳虏脨拢脟酶','潞拢碌铆脟酶','卤卤戮漏','710126','脰脨鹿煤'),(15,0,39.99,'2013-02-14','PARTIAL','chen','脦梅掳虏碌莽脳脫驴脝录录麓贸脩搂鲁陇掳虏脨拢脟酶','潞拢碌铆脟酶','卤卤戮漏','710126','脰脨鹿煤');
/*!40000 ALTER TABLE `orders` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2013-06-20 17:02:09

login.php
<?php
 require_once('book_sc_fns.php');
 do_html_header("Administration");

 display_login_form();

 do_html_footer();
?>

logout.php
<?php

// include function files for this application
require_once('book_sc_fns.php');
session_start();
$old_user = $_SESSION['admin_user'];  // store  to test if they *were* logged in
unset($_SESSION['admin_user']);
session_destroy();

// start output html
do_html_header("Logging Out");

if (!empty($old_user)) {
  echo "<p>Logged out.</p>";
  do_html_url("login.php", "Login");
} else {
  // if they weren't logged in but came to this page somehow
  echo "<p>You were not logged in, and so have not been logged out.</p>";
  do_html_url("login.php", "Login");
}

do_html_footer();

?>

order_fns.php
<?php
function process_card($card_details) {
  // connect to payment gateway or
  // use gpg to encrypt and mail or
  // store in DB if you really want to

  return true;
}

function insert_order($order_details) {
  // extract order_details out as variables
  extract($order_details);

  // set shipping address same as address
  if((!$ship_name) && (!$ship_address) && (!$ship_city) && (!$ship_state) && (!$ship_zip) && (!$ship_country)) {
    $ship_name = $name;
    $ship_address = $address;
    $ship_city = $city;
    $ship_state = $state;
    $ship_zip = $zip;
    $ship_country = $country;
  }

  $conn = db_connect();

  // we want to insert the order as a transaction
  // start one by turning off autocommit
  $conn->autocommit(FALSE);

  // insert customer address
  $query = "select customerid from customers where
            name = '".$name."' and address = '".$address."'
            and city = '".$city."' and state = '".$state."'
            and zip = '".$zip."' and country = '".$country."'";

  $result = $conn->query($query);

  if($result->num_rows>0) {
    $customer = $result->fetch_object();
    $customerid = $customer->customerid;
  } else {
    $query = "insert into customers values
            ('', '".$name."','".$address."','".$city."','".$state."','".$zip."','".$country."')";
    $result = $conn->query($query);

    if (!$result) {
      echo "insert 1 error!";
       return false;
    }
  }

  $customerid = $conn->insert_id;

  $date = date("Y-m-d");

  $query = "insert into orders values
            ('', '".$customerid."', '".$_SESSION['total_price']."', '".$date."', '".PARTIAL."',
             '".$ship_name."', '".$ship_address."', '".$ship_city."', '".$ship_state."',
             '".$ship_zip."', '".$ship_country."')";

  $result = $conn->query($query);
  if (!$result) {
    echo "insert 2 error!";
    return false;
  }

  $query = "select orderid from orders where
               customerid = '".$customerid."' and
               amount > (".$_SESSION['total_price']."-.001) and
               amount < (".$_SESSION['total_price']."+.001) and
               today_date = '".$date."' and
               order_status = 'PARTIAL' and
               ship_name = '".$ship_name."' and
               ship_address = '".$ship_address."' and
               ship_city = '".$ship_city."' and
               ship_state = '".$ship_state."' and
               ship_zip = '".$ship_zip."' and
               ship_country = '".$ship_country."'";

  $result = $conn->query($query);

  if($result->num_rows>0) {
    $order = $result->fetch_object();
    $orderid = $order->orderid;
  } else {
    echo "insert 3 error!";
    return false;
  }

  // insert each book
  foreach($_SESSION['cart'] as $isbn => $quantity) {
    $detail = get_book_details($isbn);
    $query = "delete from order_items where
              orderid = '".$orderid."' and isbn = '".$isbn."'";
    $result = $conn->query($query);
    $query = "insert into order_items values
              ('".$orderid."', '".$isbn."', ".$detail['price'].", ".$quantity.")";
    $result = $conn->query($query);
    if(!$result) {
      echo $orderid."-"; echo $isbn."-"; echo $detail['price']."-";
      echo "insert 4 error! ";
      return false;
    }
  }

  // end transaction
  $conn->commit();
  $conn->autocommit(TRUE);

  return $orderid;
}

?>

output_fns.php
<?php

function do_html_header($title = '') {
  // print an HTML header

  // declare the session variables we want access to inside the function
  if (!$_SESSION['items']) {
    $_SESSION['items'] = '0';
  }
  if (!$_SESSION['total_price']) {
    $_SESSION['total_price'] = '0.00';
  }
?>
  <html>
  <head>
    <title><?php echo $title; ?></title>
    <style>
      h2 { font-family: Arial, Helvetica, sans-serif; font-size: 22px; color: red; margin: 6px }
      body { font-family: Arial, Helvetica, sans-serif; font-size: 13px }
      li, td { font-family: Arial, Helvetica, sans-serif; font-size: 13px }
      hr { color: #FF0000; width=70%; text-align=center}
      a { color: #000000 }
    </style>
  </head>
  <body>
  <table width="100%" border="0" cellspacing="0" bgcolor="#cccccc">
  <tr>
  <td rowspan="2">
  <a href="index.php"><img src="images/Book-O-Rama.gif" alt="Bookorama" border="0"
       align="left" valign="bottom" height="55" width="325"/></a>
  </td>
  <td align="right" valign="bottom">
  <?php
     if(isset($_SESSION['admin_user'])) {
       echo "&nbsp;";
     } else {
       echo "&nbsp";
     }
  ?>
  </td>
  <td align="right" rowspan="2" width="135">
  <?php
     if(isset($_SESSION['admin_user'])) {
       display_button('logout.php', 'log-out', 'Log Out');
     } else {
       display_button('show_cart.php', 'view-cart', 'View Your Shopping Cart');
     }
  ?>
  </tr>
  <tr>
  <td align="right" valign="top">
  <?php
     if(isset($_SESSION['admin_user'])) {
       echo "&nbsp;";
     } else {
       echo "&nbsp";
     }
  ?>
  </td>
  </tr>
  </table>
<?php
  if($title) {
    do_html_heading($title);
  }
}

function do_html_footer() {
  // print an HTML footer
?>
  </body>
  </html>
<?php
}

function do_html_heading($heading) {
  // print heading
?>
  <h2><?php echo $heading; ?></h2>
<?php
}

function do_html_URL($url, $name) {
  // output URL as link and br
?>
  <a href="<?php echo $url; ?>"><?php echo $name; ?></a><br />
<?php
}

function display_categories($cat_array) {
  if (!is_array($cat_array)) {
     echo "<p>No categories currently available</p>";
     return;
  }
  echo "<ul>";
  foreach ($cat_array as $row)  {
    $url = "show_cat.php?catid=".$row['catid'];
    $title = $row['catname'];
    echo "<li>";
    do_html_url($url, $title);
    echo "</li>";
  }
  echo "</ul>";
  echo "<hr />";
}

function display_books($book_array) {
  //display all books in the array passed in
  if (!is_array($book_array)) {
    echo "<p>No books currently available in this category</p>";
  } else {
    //create table
    echo "<table width=\"100%\" border=\"0\">";

    //create a table row for each book
    foreach ($book_array as $row) {
      $url = "show_book.php?isbn=".$row['isbn'];
      echo "<tr><td>";
      if (@file_exists("images/".$row['isbn'].".jpg")) {
        $title = "<img src=\"images/".$row['isbn'].".jpg\"
                  style=\"border: 1px solid black\"/>";
        do_html_url($url, $title);
      } else {
        echo "&nbsp;";
      }
      echo "</td><td>";
      $title = $row['title']." by ".$row['author'];
      do_html_url($url, $title);
      echo "</td></tr>";
    }

    echo "</table>";
  }

  echo "<hr />";
}

function display_book_details($book) {
  // display all details about this book
  if (is_array($book)) {
    echo "<table><tr>";
    //display the picture if there is one
    if (@file_exists("images/".$book['isbn'].".jpg"))  {
      $size = GetImageSize("images/".$book['isbn'].".jpg");
      if(($size[0] > 0) && ($size[1] > 0)) {
        echo "<td><img src=\"images/".$book['isbn'].".jpg\"
              style=\"border: 1px solid black\"/></td>";
      }
    }
    echo "<td><ul>";
    echo "<li><strong>Job release:</strong> ";
    echo $book['author'];
    echo "</li><li><strong>Job number:</strong> ";
    echo $book['isbn'];
    echo "</li><li><strong>Pirce/day:</strong> ";
    echo number_format($book['price'], 2);
    echo "</li><li><strong>Description:</strong> ";
    echo $book['description'];
    echo "</li></ul></td></tr></table>";
  } else {
    echo "<p>The details of this book cannot be displayed at this time.</p>";
  }
  echo "<hr />";
}

function display_checkout_form() {
  //display the form that asks for name and address
?>
  <br />
  <table border="0" width="100%" cellspacing="0">
  <form action="purchase.php" method="post">
  <tr><th colspan="2" bgcolor="#cccccc">Your Details</th></tr>
  <tr>
    <td>Name</td>
    <td><input type="text" name="name" value="" maxlength="40" size="40"/></td>
  </tr>
  <tr>
    <td>Address</td>
    <td><input type="text" name="address" value="" maxlength="40" size="40"/></td>
  </tr>
  <tr>
    <td>City/Suburb</td>
    <td><input type="text" name="city" value="" maxlength="20" size="40"/></td>
  </tr>
  <tr>
    <td>State/Province</td>
    <td><input type="text" name="state" value="" maxlength="20" size="40"/></td>
  </tr>
  <tr>
    <td>Postal Code or Zip Code</td>
    <td><input type="text" name="zip" value="" maxlength="10" size="40"/></td>
  </tr>
  <tr>
    <td>Country</td>
    <td><input type="text" name="country" value="" maxlength="20" size="40"/></td>
  </tr>
  <tr><th colspan="2" bgcolor="#cccccc"></th></tr>
  <tr>
    <td colspan="2" align="center">
     <?php display_form_button("purchase", "Purchase These Items"); ?>
    </td>
  </tr>
  </form>
  </table><hr />
<?php
}

function display_shipping($shipping) {
  // display table row with shipping cost and total price including shipping
?>
  <table border="0" width="100%" cellspacing="0">
  <tr><td align="left">Shipping</td>
      <td align="right"> <?php echo number_format($shipping, 2); ?></td></tr>
  <tr><th bgcolor="#cccccc" align="left">TOTAL INCLUDING SHIPPING</th>
      <th bgcolor="#cccccc" align="right">$ <?php echo number_format($shipping+$_SESSION['total_price'], 2); ?></th>
  </tr>
  </table><br />
<?php
}

function display_card_form($name) {
  //display form asking for credit card details
?>
  <table border="0" width="100%" cellspacing="0">
  <form action="process.php" method="post">
  <tr><th colspan="2" bgcolor="#cccccc">Credit Card Details</th></tr>
  <tr>
    <td>Type</td>
    <td><select name="card_type">
        <option value="VISA">VISA</option>
        <option value="MasterCard">MasterCard</option>
        <option value="American Express">American Express</option>
        </select>
    </td>
  </tr>
  <tr>
    <td>Number</td>
    <td><input type="text" name="card_number" value="" maxlength="16" size="40"></td>
  </tr>
  <tr>
    <td>AMEX code (if required)</td>
    <td><input type="text" name="amex_code" value="" maxlength="4" size="4"></td>
  </tr>
  <tr>
    <td>Expiry Date</td>
    <td>Month
       <select name="card_month">
       <option value="01">01</option>
       <option value="02">02</option>
       <option value="03">03</option>
       <option value="04">04</option>
       <option value="05">05</option>
       <option value="06">06</option>
       <option value="07">07</option>
       <option value="08">08</option>
       <option value="09">09</option>
       <option value="10">10</option>
       <option value="11">11</option>
       <option value="12">12</option>
       </select>
       Year
       <select name="card_year">
       <?php
       for ($y = date("Y"); $y < date("Y") + 10; $y++) {
         echo "<option value=\"".$y."\">".$y."</option>";
       }
       ?>
       </select>
  </tr>
  <tr>
    <td>Name on Card</td>
    <td><input type="text" name="card_name" value = "<?php echo $name; ?>" maxlength="40" size="40"></td>
  </tr>
  <tr>
    <td colspan="2" align="center">
      <p><strong>Please press Purchase to confirm your purchase, or Continue Shopping to
      add or remove items</strong></p>
     <?php display_form_button('purchase', 'Purchase These Items'); ?>
    </td>
  </tr>
  </table>
<?php
}

function display_cart($cart, $change = true, $images = 1) {
  // display items in shopping cart
  // optionally allow changes (true or false)
  // optionally include images (1 - yes, 0 - no)

   echo "<table border=\"0\" width=\"100%\" cellspacing=\"0\">
         <form action=\"show_cart.php\" method=\"post\">
         <tr><th colspan=\"".(1 + $images)."\" bgcolor=\"#cccccc\">Job</th>
         <th bgcolor=\"#cccccc\">Price/day</th>
         <th bgcolor=\"#cccccc\">Quantity</th>
         <th bgcolor=\"#cccccc\">Total</th>
         </tr>";

  //display each item as a table row
  foreach ($cart as $isbn => $qty)  {
    $book = get_book_details($isbn);
    echo "<tr>";
    if($images == true) {
      echo "<td align=\"left\">";
      if (file_exists("images/".$isbn.".jpg")) {
         $size = GetImageSize("images/".$isbn.".jpg");
         if(($size[0] > 0) && ($size[1] > 0)) {
           echo "<img src=\"images/".$isbn.".jpg\"
                  style=\"border: 1px solid black\"
                  width=\"".($size[0]/3)."\"
                  height=\"".($size[1]/3)."\"/>";
         }
      } else {
         echo "&nbsp;";
      }
      echo "</td>";
    }
    echo "<td align=\"left\">
          <a href=\"show_book.php?isbn=".$isbn."\">".$book['title']."</a>
          by ".$book['author']."</td>
          <td align=\"center\">\$".number_format($book['price'], 2)."</td>
          <td align=\"center\">";

    // if we allow changes, quantities are in text boxes
    if ($change == true) {
      //echo "<input type=\"text\" name=\"".$isbn."\" value=\"".$qty."\" size=\"3\">";
      echo "1";
    } else {
      echo "1";
    }
    echo "</td><td align=\"center\">\$".number_format($book['price'],2)."</td></tr>\n";
  }
  // display total row
  echo "<tr>
        <th colspan=\"".(2+$images)."\" bgcolor=\"#cccccc\">&nbsp;</td>
        <th align=\"center\" bgcolor=\"#cccccc\"></th>
        <th align=\"center\" bgcolor=\"#cccccc\">
            
        </th>
        </tr>";

  // display save change button
  if($change == true) {
    // echo "<tr>
    //       <td colspan=\"".(2+$images)."\">&nbsp;</td>
    //       <td align=\"center\">
    //          <input type=\"hidden\" name=\"save\" value=\"true\"/>
    //          <input type=\"image\" src=\"images/save-changes.gif\"
    //                 border=\"0\" alt=\"Save Changes\"/>
    //       </td>
    //       <td>&nbsp;</td>
    //       </tr>";
  }
  echo "</form></table>";
}

function display_login_form() {
  // dispaly form asking for name and password
?>
 <form method="post" action="admin.php">
 <table bgcolor="#cccccc">
   <tr>
     <td>Username:</td>
     <td><input type="text" name="username"/></td></tr>
   <tr>
     <td>Password:</td>
     <td><input type="password" name="passwd"/></td></tr>
   <tr>
     <td colspan="2" align="center">
     <input type="submit" value="Log in"/></td></tr>
   <tr>
 </table></form>
<?php
}

function display_admin_menu() {
?>
<br />
<a href="index.php">Go to main site</a><br />
<a href="insert_category_form.php">Add a new category</a><br />
<a href="insert_book_form.php">Add a new part-time job</a><br />
<a href="change_password_form.php">Change admin password</a><br />
<?php
}

function display_button($target, $image, $alt) {
  echo "<div align=\"center\"><a href=\"".$target."\">
          <img src=\"images/".$image.".gif\"
           alt=\"".$alt."\" border=\"0\" height=\"50\"
           width=\"135\"/></a></div>";
}

function display_form_button($image, $alt) {
  echo "<div align=\"center\"><input type=\"image\"
           src=\"images/".$image.".gif\"
           alt=\"".$alt."\" border=\"0\" height=\"50\"
           width=\"135\"/></div>";
}

?>

populate.sql
USE book_sc;


INSERT INTO books VALUES ('0672329166','Luke Welling and Laura Thomson','PHP and MySQL Web Development',1,49.99,
'PHP & MySQL Web Development teaches the reader to develop dynamic, secure e-commerce web sites. You will learn to integrate and implement these technologies by following real-world examples and working sample projects.');
INSERT INTO books VALUES ('067232976X','Julie Meloni','Sams Teach Yourself PHP, MySQL and Apache All-in-One',1,34.99,
'Using a straightforward, step-by-step approach, each lesson in this book builds on the previous ones, enabling you to learn the essentials of PHP scripting, MySQL databases, and the Apache web server from the ground up.');
INSERT INTO books VALUES ('0672319241','Sterling Hughes and Andrei Zmievski','PHP Developer\'s Cookbook',1,39.99,
'Provides a complete, solutions-oriented guide to the challenges most often faced by PHP developers\r\nWritten specifically for experienced Web developers, the book offers real-world solutions to real-world needs\r\n');

INSERT INTO categories VALUES (1,'Internet');
INSERT INTO categories VALUES (2,'Self-help');
INSERT INTO categories VALUES (5,'Fiction');
INSERT INTO categories VALUES (4,'Gardening');

INSERT INTO admin VALUES ('admin', sha1('admin'));

process.php
<?php
  include ('book_sc_fns.php');
  // The shopping cart needs sessions, so start one
  session_start();

  do_html_header('Checkout');

  $card_type = $_POST['card_type'];
  $card_number = $_POST['card_number'];
  $card_month = $_POST['card_month'];
  $card_year = $_POST['card_year'];
  $card_name = $_POST['card_name'];

  if(($_SESSION['cart']) && ($card_type) && ($card_number) &&
     ($card_month) && ($card_year) && ($card_name)) {
    //display cart, not allowing changes and without pictures
    display_cart($_SESSION['cart'], false, 0);

    display_shipping(calculate_shipping_cost());

    if(process_card($_POST)) {
      //empty shopping cart
      session_destroy();
      echo "<p>Thank you for shopping with us. Your order has been placed.</p>";
      display_button("index.php", "continue-shopping", "Continue Shopping");
    } else {
      echo "<p>Could not process your card. Please contact the card issuer or try again.</p>";
      display_button("purchase.php", "back", "Back");
    }
  } else {
    echo "<p>You did not fill in all the fields, please try again.</p><hr />";
    display_button("purchase.php", "back", "Back");
  }

  do_html_footer();
?>

purchase.php
<?php

  include ('book_sc_fns.php');
  // The shopping cart needs sessions, so start one
  session_start();

  do_html_header("Checkout");

  // create short variable names
  $name = $_POST['name'];
  $address = $_POST['address'];
  $city = $_POST['city'];
  $zip = $_POST['zip'];
  $country = $_POST['country'];

  // if filled out
  if (($_SESSION['cart']) && ($name) && ($address) && ($city) && ($zip) && ($country)) {
    // able to insert into database
    if(insert_order($_POST) != false ) {
      //display cart, not allowing changes and without pictures
      display_cart($_SESSION['cart'], false, 0);

      display_shipping(calculate_shipping_cost());

      //get credit card details
      display_card_form($name);

      display_button("show_cart.php", "continue-shopping", "Continue Shopping");
    } else {
      echo "<p>Could not store data, please try again.</p>";
      display_button('checkout.php', 'back', 'Back');
    }
  } else {
    echo "<p>You did not fill in all the fields, please try again.</p><hr />";
    display_button('checkout.php', 'back', 'Back');
  }

  do_html_footer();
?>

show_book.php
<?php
  include ('book_sc_fns.php');
  // The shopping cart needs sessions, so start one
  session_start();

  $isbn = $_GET['isbn'];

  // get this book out of database
  $book = get_book_details($isbn);
  do_html_header($book['title']);
  display_book_details($book);

  // set url for "continue button"
  $target = "index.php";
  if($book['catid']) {
    $target = "show_cat.php?catid=".$book['catid'];
  }

  // if logged in as admin, show edit book links
  if(check_admin_user()) {
    display_button("edit_book_form.php?isbn=".$isbn, "edit-item", "Edit Item");
    display_button("admin.php", "admin-menu", "Admin Menu");
    display_button($target, "continue", "Continue");
  } else {
    display_button("show_cart.php?new=".$isbn, "add-to-cart",
                   "Add".$book['title']." To My Shopping Cart");
    display_button($target, "continue-shopping", "Continue Shopping");
  }

  do_html_footer();
?>

show_cart.php
<?php
  include ('book_sc_fns.php');
  // The shopping cart needs sessions, so start one
  session_start();

  @$new = $_GET['new'];

  if($new) {
    //new item selected
    if(!isset($_SESSION['cart'])) {
      $_SESSION['cart'] = array();
      $_SESSION['items'] = 0;
      $_SESSION['total_price'] ='0.00';
    }

    if(isset($_SESSION['cart'][$new])) {
      $_SESSION['cart'][$new]++;
    } else {
      $_SESSION['cart'][$new] = 1;
    }

    $_SESSION['total_price'] = calculate_price($_SESSION['cart']);
    $_SESSION['items'] = calculate_items($_SESSION['cart']);
  }

  if(isset($_POST['save'])) {
    foreach ($_SESSION['cart'] as $isbn => $qty) {
      if($_POST[$isbn] == '0') {
        unset($_SESSION['cart'][$isbn]);
      } else {
        $_SESSION['cart'][$isbn] = $_POST[$isbn];
      }
    }

    $_SESSION['total_price'] = calculate_price($_SESSION['cart']);
    $_SESSION['items'] = calculate_items($_SESSION['cart']);
  }

  do_html_header("Your jobs view");

  if(($_SESSION['cart']) && (array_count_values($_SESSION['cart']))) {
    display_cart($_SESSION['cart']);
  } else {
    echo "<p>There are no jobs</p><hr/>";
  }

  $target = "index.php";

  // if we have just added an item to the cart, continue shopping in that category
  if($new)   {
    $details =  get_book_details($new);
    if($details['catid']) {
      $target = "show_cat.php?catid=".$details['catid'];
    }
  }
  display_button($target, "continue", "Continue");

  // use this if SSL is set up
  // $path = $_SERVER['PHP_SELF'];
  // $server = $_SERVER['SERVER_NAME'];
  // $path = str_replace('show_cart.php', '', $path);
  // display_button("https://".$server.$path."checkout.php",
  //                 "go-to-checkout", "Go To Checkout");

  // if no SSL use below code
  display_button("checkout.php", "go-to-checkout", "Go To Checkout");

  do_html_footer();
?>

show_cat.php
<?php
  include ('book_sc_fns.php');
  // The shopping cart needs sessions, so start one
  session_start();

  $catid = $_GET['catid'];
  $name = get_category_name($catid);

  do_html_header($name);

  // get the book info out from db
  $book_array = get_books($catid);

  display_books($book_array);


  // if logged in as admin, show add, delete book links
  if(isset($_SESSION['admin_user'])) {
    display_button("index.php", "continue", "Continue Shopping");
    display_button("admin.php", "admin-menu", "Admin Menu");
    display_button("edit_category_form.php?catid=".$catid,
                   "edit-category", "Edit Category");
  } else {
    display_button("index.php", "continue-shopping", "Continue Shopping");
  }

  do_html_footer();
?>

user_auth_fns.php
<?php

require_once('db_fns.php');

function login($username, $password) {
// check username and password with db
// if yes, return true
// else return false

  // connect to db
  $conn = db_connect();
  if (!$conn) {
    return 0;
  }

  // check if username is unique
  $result = $conn->query("select * from admin
                         where username='".$username."'
                         and password = sha1('".$password."')");
  if (!$result) {
     return 0;
  }

  if ($result->num_rows>0) {
     return 1;
  } else {
     return 0;
  }
}

function check_admin_user() {
// see if somebody is logged in and notify them if not

  if (isset($_SESSION['admin_user'])) {
    return true;
  } else {
    return false;
  }
}

function change_password($username, $old_password, $new_password) {
// change password for username/old_password to new_password
// return true or false

  // if the old password is right
  // change their password to new_password and return true
  // else return false
  if (login($username, $old_password)) {

    if (!($conn = db_connect())) {
      return false;
    }

    $result = $conn->query("update admin
                            set password = sha1('".$new_password."')
                            where username = '".$username."'");
    if (!$result) {
      return false;  // not changed
    } else {
      return true;  // changed successfully
    }
  } else {
    return false; // old password was wrong
  }
}


?>


