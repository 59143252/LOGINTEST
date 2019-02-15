# LOGINTEST
LOGINTEST
<?php require_once('Connections/kuupfaa.php'); ?>
<?php
if (!function_exists("GetSQLValueString")) {
function GetSQLValueString($theValue, $theType, $theDefinedValue = "", $theNotDefinedValue = "") 
{
  if (PHP_VERSION < 6) {
    $theValue = get_magic_quotes_gpc() ? stripslashes($theValue) : $theValue;
  }

  $theValue = function_exists("mysql_real_escape_string") ? mysql_real_escape_string($theValue) : mysql_escape_string($theValue);

  switch ($theType) {
    case "text":
      $theValue = ($theValue != "") ? "'" . $theValue . "'" : "NULL";
      break;    
    case "long":
    case "int":
      $theValue = ($theValue != "") ? intval($theValue) : "NULL";
      break;
    case "double":
      $theValue = ($theValue != "") ? doubleval($theValue) : "NULL";
      break;
    case "date":
      $theValue = ($theValue != "") ? "'" . $theValue . "'" : "NULL";
      break;
    case "defined":
      $theValue = ($theValue != "") ? $theDefinedValue : $theNotDefinedValue;
      break;
  }
  return $theValue;
}
}
?>
<?php
// *** Validate request to login to this site.
if (!isset($_SESSION)) {
  session_start();
}

$loginFormAction = $_SERVER['PHP_SELF'];
if (isset($_GET['accesscheck'])) {
  $_SESSION['PrevUrl'] = $_GET['accesscheck'];
}

if (isset($_POST['texrUser'])) {
  $loginUsername=$_POST['texrUser'];
  $password=md5($_POST['textPass']);
  $MM_fldUserAuthorization = "";
  $MM_redirectLoginSuccess = "MD5.php";
  $MM_redirectLoginFailed = "erroe.php";
  $MM_redirecttoReferrer = false;
  mysql_select_db($database_kuupfaa, $kuupfaa);
  
  $LoginRS__query=sprintf("SELECT Username, password FROM `user` WHERE Username=%s AND password=%s",
    GetSQLValueString($loginUsername, "text"), GetSQLValueString($password, "text")); 
   
  $LoginRS = mysql_query($LoginRS__query, $kuupfaa) or die(mysql_error());
  $loginFoundUser = mysql_num_rows($LoginRS);
  if ($loginFoundUser) {
     $loginStrGroup = "";
    
    //declare two session variables and assign them
    $_SESSION['MM_Username'] = $loginUsername;
    $_SESSION['MM_UserGroup'] = $loginStrGroup;	      

    if (isset($_SESSION['PrevUrl']) && false) {
      $MM_redirectLoginSuccess = $_SESSION['PrevUrl'];	
    }
    header("Location: " . $MM_redirectLoginSuccess );
  }
  else {
    header("Location: ". $MM_redirectLoginFailed );
  }
}
?>
<form name="form1" method="POST" action="<?php echo $loginFormAction; ?>">
  <table width="447" height="101" border="1">
    <tr>
      <th width="208" height="30">Username</th>
      <td width="274"><label>
        <input type="text" name="texrUser" id="texrUser">
      </label></td>
    </tr>
    <tr>
      <th height="27">Password</th>
      <td><label>
        <input type="password" name="textPass" id="textPass">
      </label></td>
    </tr>
    <tr>
      <td height="34">&nbsp;</td>
      <td><label>
        <input type="submit" name="btnlogin" id="button" value="Login">
      </label></td>
    </tr>
  </table>
</form>
