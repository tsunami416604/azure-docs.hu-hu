<properties
   pageTitle="為 Web 應用程式有效地使用 DevOps 環境"
   description="了解如何使用部署位置來設定和管理應用程式的多個開發環境"
   services="app-service\web"
   documentationCenter=""
   authors="sunbuild"
   manager="yochayk"
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web"
   ms.date="09/24/2015"
   ms.author="sumuth"/>


# 為 Web 應用程式有效地使用 DevOps 環境

本文將告訴您安裝和管理您的應用程式，例如開發、 預備、 品管與製造的多個版本的 web 應用程式部署。 您的應用程式的每個版本可以視為您的部署程序內的特定需求的開發環境，例如品管環境可以由您的開發人員小組用來測試應用程式的品質，之後才將變更推入至生產環境。
設定多個開發環境可能是具挑戰性的工作，因為您需要追蹤、管理資源 (運算、Web 應用程式、資料庫、快取等)。 在這些環境上及在不同環境之間部署內容。

## 設定非生產環境 (預備、開發、品管)

您的生產 Web 應用程式啟動並執行之後，下一步是建立非生產環境。 為了使用部署位置，請確定您執行 **Standard** 或 **Premium** App Service 方案模式。 部署位置實際上是含有自己主機名稱的作用中 Web 應用程式。 兩個部署位置 (包括生產位置) 之間的 Web 應用程式內容與設定項目可以互相交換。 將應用程式部署至部署位置具有下列優點：

1. 您可以先驗證預備部署位置中的 Web 應用程式變更，再將它與生產位置進行交換。
2. 先將 Web 應用程式部署至某個位置，然後再將它交換到生產位置，可確保該位置的所有執行個體在交換到生產位置之前都已準備就緒。 這麼做可排除部署 Web 應用程式時的停機情況。 交換作業期間所有的流量都能順暢地重新導向，而且不會捨棄任何要求封包。 這整個工作流程可自動化設定 [自動交換](web-sites-staged-publishing/#configure-auto-swap-for-your-web-app) 不需要預先交換驗證時。
3. 交換之後，含有之前預備 Web 應用程式的位置，現已擁有之前的生產 Web 應用程式。 若交換到生產位置的變更不是您需要的變更，您可以立即執行相同的交換，以取回「上一個已知良好的 Web 應用程式」。

若要設定預備部署位置，請參閱 [設定預備環境的 web 應用程式在 Azure App Service 中](web-sites-staged-publishing) 。 每個環境應該包含其自己的一組資源，例如，如果您的 Web 應用程式使用資料庫，則生產和預備 Web 應用程式應該使用不同的資料庫。 加入預備開發環境資源，例如資料庫、儲存體或快取，用於設定您的預備開發環境。

## 使用多個開發環境的範例

任何專案應該遵循原始程式碼管理具有兩個以上的環境、 開發和生產環境時使用的內容管理系統，但應用程式架構等等，我們可能會遇到問題的應用程式不支援這種情況下，現成的。 以下討論的部分熱門架構正是如此。 使用 CMS/架構時會浮現很多問題，例如

1. 如何分散到不同的環境
2. 我可以變更哪些檔案並且不會影響架構版本更新
3. 如何管理每個環境的組態
4. 如何管理模組/外掛程式版本更新、核心架構版本更新

有許多種方法可為您的專案設定多個環境，而以下只是個別應用程式這類方法的範例。

### WordPress

在本節中，您將學習如何使用 WordPress 位置來設定部署工作流程。 WordPress 如同大部分的 CMS 解決方案，並不預設支援使用多個開發環境。 App Service Web Apps 有一些功能可讓您更輕鬆地將組態設定儲存在您的程式碼之外。

建立預備位置之前，請設定您的應用程式程式碼來支援多個環境。 若要支援多個環境中您要編輯的 WordPress `wp-config.php` 本機開發 web 應用程式上，請在檔案開頭加入下列程式碼。 這會讓您的應用程式根據所選環境挑選正確的組態。

```
// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
    $config_file = 'config/wp-config.local.php';
}
elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
      //single file for all azure development environments
      $config_file = 'config/wp-config.azure.php';
}
$path = dirname(__FILE__) . '/';
if (file_exists($path . $config_file)) {
// include the config file if it exists, otherwise WP is going to fail
require_once $path . $config_file;
```

呼叫的 web 應用程式根目錄下建立資料夾 `config` ，並加入第二個檔案:  `wp config.azure.php`  和 `wp config.local.php`  分別代表您的 azure 和本機環境。

複製下列 `wp config.local.php` :

```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', 'yourdatabasename');

/** MySQL database username */
define('DB_USER', 'yourdbuser');

/** MySQL database password */
define('DB_PASSWORD', 'yourpassword');

/** MySQL hostname */
define('DB_HOST', 'localhost');
/**
 * For developers: WordPress debugging mode.
 * * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', true);

//Security key settings
define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';
```

設定上述的安全性金鑰可以協助防止您的 Web 應用程式受到駭客攻擊，因此請使用唯一值。 如果您需要產生的字串，如先前所述的安全性金鑰，您即可建立新索引鍵/值使用此 [連結] (https://api.wordpress.org/secret-key/1.1/salt) 自動產生器

複製下列程式碼中的 `wp config.azure.php`:


```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', getenv('DB_NAME'));

/** MySQL database username */
define('DB_USER', getenv('DB_USER'));

/** MySQL database password */
define('DB_PASSWORD', getenv('DB_PASSWORD'));

/** MySQL hostname */
define('DB_HOST', getenv('DB_HOST'));

/**
* For developers: WordPress debugging mode.
*
* Change this to true to enable the display of notices during development.
* It is strongly recommended that plugin and theme developers use WP_DEBUG
* in their development environments.
* Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
* do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
* with WP_DEBUG_LOG so that errors are not displayed on the page */

*/
define('WP_DEBUG', getenv('WP_DEBUG'));
define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
define('WP_DEBUG_DISPLAY',false);

//Security key settings
/** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
define('AUTH_KEY' ,getenv('DB_AUTH_KEY'));
define('SECURE_AUTH_KEY',  getenv('DB_SECURE_AUTH_KEY'));
define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
define('NONCE_KEY', getenv('DB_NONCE_KEY'));
define('AUTH_SALT',  getenv('DB_AUTH_SALT'));
define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
define('LOGGED_IN_SALT',   getenv('DB_LOGGED_IN_SALT'));
define('NONCE_SALT',   getenv('DB_NONCE_SALT'));

/**
* WordPress Database Table prefix.
*
* You can have multiple installations in one database if you give each a unique
* prefix. Only numbers, letters, and underscores please!
*/
$table_prefix  = getenv('DB_PREFIX');
```

#### 使用相對路徑

最後一件事是允許 WordPress 應用程式使用相對路徑。 WordPress 會在資料庫中儲存 URL 資訊。 這會使得在不同環境間移動內容變得困難，因為每次從本機到預備或預備到生產環境移動時，都需要更新資料庫。 若要減少部署資料庫，每次您從一個環境部署至另一種用法會造成的問題的風險 [相對根連結外掛程式](https://wordpress.org/plugins/root-relative-urls/) 可以使用 WordPress 管理員儀表板來安裝或從手動下載 [這裡](https://downloads.wordpress.org/plugin/root-relative-urls.zip)。

新增下列項目至您 `wp-config.php` 檔案之後，再 `That's all，停止編輯!` 註解:

```
    define('WP_HOME', 'http://' . $_SERVER ['HTTP_HOST']);
    define('WP_SITEURL', 'http://' . $_SERVER ['HTTP_HOST']);
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);
```
啟用透過外掛程式 `外掛程式` WordPress 管理員儀表板中的功能表。 儲存您的永久連結設定供 WordPress 應用程式使用。

#### 最終 `wp-config.php` 檔案

任何 WordPress 核心更新將不會影響您 `wp-config.php` , ，`wp config.azure.php` 和 `wp config.local.php` 檔案。 最後這篇 how `wp-config.php` 檔案看起來像這樣

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
    $config_file = 'config/wp-config.local.php';
}
elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
    $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__) . '/';
if (file_exists($path . $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path . $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__) . '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
```

#### 設定預備環境

假設您已經登入 Azure 網站上執行的 WordPress web 應用程式 [Azure 入口網站](http://portal.azure.com) 並移至您的 WordPress web 應用程式。 如果沒有，您可以從 Marketplace 建立一個。 若要深入了解，按一下 [這裡](web-sites-php-web-site-gallery)。
按一下 [設定]-> [部署位置]-> [加入] 以建立具有 stage 名稱的部署位置。部署位置是與以上建立的主要的 Web 應用程式共用相同的資源的另一個 Web 應用程式。

![建立階段部署位置](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

新增其他 MySQL 資料庫中，假設 `wordpress-階段 db` 至資源群組 `wordpressapp 群組`。

 ![將 MySQL 資料庫加入至資源群組](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

更新您預備部署位置，以指向新建立的資料庫連接字串 `wordpress-階段 db`。 請注意您的生產 web 應用程式中， `wordpressapp 群組` 預備 web 應用程式和 `wordpressprodapp 階段` 必須指向不同的資料庫。

#### 設定環境特定的應用程式設定

開發人員可以隨著與 Web 應用程式相關聯的組態資訊 (稱為「應用程式設定」) 在 Azure 中儲存索引鍵-值字串組。 在執行階段，App Service Web Apps 會為您自動擷取這些值，並使值可供您的 Web 應用程式中執行的程式碼使用。 安全性是不錯的側邊的角度來看受益自機密資訊如資料庫連接字串，以及密碼永遠不會顯示為純文字檔案中例如 `wp-config.php`。

下面定義此程序很有用，因為它包含變更檔案和資料庫變更為 WordPress 應用程式執行時:

- WordPress 版本升級
- 加入新的或編輯或升級外掛程式
- 加入新的或編輯或升級佈景主題

設定下列項目的應用程式設定：

- 資料庫資訊
- 開啟/關閉 WordPress 記錄
- WordPress 安全性設定

![Wordpress Web 應用程式的應用程式設定](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

請確定您已為您的生產 Web 應用程式和預備位置加入下列應用程式設定。 請注意，實際執行 Web 應用程式和預備 Web 應用程式使用不同的資料庫。
取消核取 WP_ENV 以外的所有設定參數的 [位置設定]**** 核取方塊。 這將會交換 Web 應用程式的組態，以及檔案內容和資料庫。 如果 [位置設定]******已勾選**，執行交換操作時，Web 應用程式的應用程式設定、連接字串組態將「不會」跨環境移動，因此如果有任何資料庫變更，在中斷您的生產 Web 應用程式時將看不到該變更。

使用 WebMatrix 或您選擇的工具 (例如 FTP、Git、PhpMyAdmin)，將本機開發環境 Web 應用程式部署到預備 Web 應用程式和資料庫。

![WordPress Web 應用程式的 WebMatrix 發佈對話方塊](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

瀏覽及測試您的預備 Web 應用程式。 考慮要更新 Web 應用程式的佈景主題案例，以下是預備的 Web 應用程式。

![交換位置之前瀏覽預備 Web 應用程式](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 如果一切看起來皆正常，在預備 Web 應用程式設定上按一下 [交換]**** 按鈕，將您的內容移到生產環境上。 在此情況下，您會在每個**交換**作業期間交換環境之間的 Web 應用程式和資料庫。

![交換 WordPress 的變更的預覽](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)
 > [AZURE.NOTE]
 >如果您有的案例，您必須只推入的檔案 (資料庫更新)，然後 **檢查**  **位置設定** 相關的所有資料庫 *應用程式設定* 和 *連接字串設定* Azure 入口網站，然後再執行交換內的 web 應用程式設定刀鋒視窗中。 在此情況下，DB_NAME、DB_HOST、DB_PASSWORD、DB_USER 預設連接字串設定在執行**交換**的時候應該不會顯示在預覽變更中。 在此時，當您完成**交換**作業，WordPress Web 應用程式將**只會**有更新檔案。

在執行之前交換，以下是生產 WordPress web 應用程式
![交換位置之前的生產 Web 應用程式](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

交換作業之後，佈景主題已在您的生產 Web 應用程式上更新。

![交換位置之後的生產 Web 應用程式](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

當您需要**回復**的情況下，您可以移至生產 Web 應用程式設定，並按一下 [交換]**** 按鈕，將 Web 應用程式與資料庫從生產交換至預備位置。 要記住的重點是，如果資料庫的變更是在給定時間隨著**交換**作業納入，那麼，在下次重新部署至預備 Web 應用程式時，您需要將資料庫變更部署到預備 Web 應用程式所指向的目前資料庫，而該資料庫先前可能為生產或預備資料庫。

#### 摘要

將具備資料庫的任何應用程式的程序一般化

1. 在本機環境上安裝應用程式
2. 包含環境特定組態 (本機和 Azure Web 應用程式)
3. 設定在應用程式服務 Web 應用程式 – 準備階段，實際執行環境
4. 如果您已經在 Azure 上執行生產應用程式，請將您的生產內容 (檔案/程式碼 + 資料庫) 同步到本機和預備環境。
5. 在您的本機環境上開發應用程式
6. 將內容從實際執行環境和開發環境下維護或鎖定的模式與同步處理資料庫的生產 web 應用程式
7. 將部署到預備環境和測試
8. 部署到生產環境
9. 重複步驟 4 到 6

### Umbraco

在本節中，您將學習 Umbraco CMS 如何使用自訂模組跨多個 DevOps 環境進行部署。此範例為您提供管理多個開發環境的不同方法。

[Umbraco CMS](http://umbraco.com/) 是受歡迎.NET CMS 解決方案由許多開發人員可提供的其中一個 [Courier2](http://umbraco.com/products/more-add-ons/courier-2) 模組將從開發到預備環境到生產環境。 您可以使用 Visual Studio 或 WebMatrix，輕鬆地建立 Umbraco CMS Web 應用程式的本機開發環境。

1. 使用 Visual Studio 建立 Umbraco web 應用程式中，按一下 [這裡](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget) 。
2. 若要使用 WebMatrix 建立 Umbraco web 應用程式，請按一下 [ [這裡](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix)。

一定要記得移除 `安裝` 資料夾下您的應用程式，但絕不會將它上載至階段或實際執行 web 應用程式。 在本教學課程中，我將使用 WebMatrix

#### 設定預備環境

如上所述為 Umbraco CMS Web 應用程式建立部署位置，假設您已有 Umbraco CMS Web 應用程式運作且執行中。 如果沒有，您可以從 Marketplace 建立一個。

將您的預備部署位置的連接字串更新，以指向新建立的資料庫 **umbraco-stage-db**。 您的生產 Web 應用程式 (umbraositecms-1) 和預備 Web 應用程式 (umbracositecms-1-stage) **必須**指向不同的資料庫。

![使用新預備資料庫更新預備 Web 應用程式的連接字串](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

按一下 [  **取得發行設定** 部署位置 **階段**。 這會下載發佈設定檔，它可儲存 Visual Studio 或 WebMatrix 從本機開發 Web 應用程式將您的應用程式發佈到 Azure Web 應用程式所需的所有資訊。

 ![取得預備 Web 應用程式的發佈設定](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- 在 **WebMatrix** 或 **Visual Studio** 中開啟您的本機開發 Web 應用程式。 在本教學課程中，我會使用 WebMatrix，並且需要您先為您的預備 Web 應用程式匯入發佈設定檔

![使用 WebMatrix 匯入 Umbraco 的發佈設定](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- 在對話方塊中檢閱變更，並將本機 Web 應用程式部署至您的 Azure Web 應用程式 *umbracositecms-1-stage*。 當您將檔案部署至預備 web 應用程式直接將略過中的任何檔案 `~/app_data/TEMP/` 資料夾，這些將會重新產生第一個階段的 web 應用程式時啟動。 您也應該省略 `~/app_data/umbraco.config` ，檔案做為此，我們會重新產生。

![在 WebMatrix 中檢閱發佈變更](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- 已成功發佈 Umbraco 本機 Web 應用程式至預備 Web 應用程式之後，瀏覽預備 Web 應用程式，並執行一些測試來排除任何問題。

#### 設定 Courier2 部署模組

使用 [Courier2](http://umbraco.com/products/more-add-ons/courier-2) 模組可以推送內容、 樣式表、 開發模組，以及與按一下滑鼠右鍵從預備 web 應用程式至實際執行 web 應用程式有更多的麻煩免費部署，並減少部署更新時，中斷生產 web 應用程式的風險。
購買的授權網域 Courier2 `*。 azurewebsites.net` 和您的自訂網域 (例如 http://abc.com) 一旦您已購買的授權，將下載的授權 (。授權檔) 中 `bin` 資料夾。

![將授權檔案拖放到 bin 資料夾下](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

立即下載 Courier2 套件從 [這裡](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/) 。 登入 web 應用程式階段，說出 http://umbracocms-site-stage.azurewebsites.net/umbraco 並按一下 [ **開發人員** 功能表，然後選取 **封裝** 。 按一下 [安裝]**** 本機封裝

![Umbraco 套件安裝程式](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

使用安裝程式將 courier2 套件上傳。

![上傳 courier 模組的套件](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

若要設定您需要更新下的 courier.config 檔案  **Config** web 應用程式的資料夾。

```xml

  
  <repositories>
        
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1.azurewebsites.net</url>
            <user>0</user>
            
            
           
           </repository>
  </repositories>
```

在 `< 存放庫 >`, ，輸入實際執行網站的 URL 和使用者資訊。如果您使用預設 Umbraco 成員資格提供者，則將中的系統管理使用者的識別碼 <user> 一節。如果您使用自訂的 Umbraco 成員資格提供者，使用 `< 登入 >`,，`< 密碼 >` Courier2 模組已了解如何連接到實際執行網站。如需詳細資訊，請檢閱 [文件](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) 新細明體模組。

同樣地，您的生產網站上安裝新細明體模組和設定階段的 web 應用程式指向其各自的 courier.config 檔案如下所示

```xml
  
  
  <repositories>
        
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1-stage.azurewebsites.net</url>
            <user>0</user>
           </repository>
  </repositories>
```

按一下 Courier2 Umbraco CMS web 應用程式儀表板] 索引標籤上，並選取位置。 您可以看到儲存機制名稱中所述 `courier.config`。在生產和預備 web 應用程式上執行這項操作。

![檢視目的地 Web 應用程式儲存機制](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

現在讓我們從預備網站部署一些內容至生產網站。 移至內容並選取現有的網頁或建立新頁面。 我將從我的 Web 應用程式選取現有的網頁，其中頁面的標題會變更為**開始使用 - 新增**，現在按一下 [儲存並發佈]****。

![變更頁面的標題並發佈](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

現在選取已修改的頁面，然後*以滑鼠右鍵按一下*來檢視所有選項。 按一下 [Courier]**** 來檢視 [部署] 對話方塊。 按一下 [部署]**** 來起始部署。

![Courier 模組部署對話方塊](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

檢閱變更並按一下 [繼續]。

![Courier 模組部署對話方塊檢閱變更](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

部署記錄檔會顯示部署是否成功。

 ![檢視 Courier 模組的部署記錄檔](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

瀏覽您的生產 Web 應用程式以查看是否反映了變更。

 ![瀏覽實際執行 Web 應用程式](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

若要深入了解如何使用 Courier，請檢閱文件集。

#### 如何升級 Umbraco CMS 版本

Courier 將不會隨著從一個版本的 Umbraco CMS 升級至另一個版本部署說明。 升級 Umbraco CMS 版本時，您必須檢查與您的自訂模組或協力廠商模組和 Umbraco 核心程式庫的不相容項目。 最佳作法

1. 務必在進行升級之前備份您的 Web 應用程式和資料庫。 在 Azure Web 應用程式上，您可以使用備份功能設定網站的自動備份，並在需要時使用還原功能還原您的網站。 如需詳細資訊，請參閱 [如何備份您的 web 應用程式](web-sites-backup) 和 [如何將 web 應用程式還原](web-sites-restore)。

2. 檢查您正在使用的第三方封裝是否與您要升級的版本相容。 在封裝的下載頁面，檢閱具有 Umbraco CMS 版本的專案相容性。

如需有關如何升級 web 應用程式在本機，遵循的指導方針，如前所述 [這裡](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general)。

一旦升級您的本機開發網站，將變更發佈至預備 Web 應用程式。 測試您的應用程式，而如果一切看起來皆正常，請使用 [交換]**** 按鈕，將預備網站**交換**為生產 Web 應用程式。 執行**交換**作業時，您可以檢視將在您的 Web 應用程式組態中受到影響的變更。 利用這個**交換**作業，我們會交換 Web Apps 和資料庫。 這表示，交換後，實際執行 Web 應用程式現在會指向 umbraco-stage-db 資料庫，而預備 Web 應用程式會指向 umbraco-prod-db 資料庫。

![交換部署 Umbraco CMS 的預覽](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

交換 web 應用程式和資料庫的優點:
1. 如果發生任何應用程式問題，能讓您利用另一個**交換**回復至舊版的 Web 應用程式。
2. 針對升級，您需要將來自預備 Web 應用程式的檔案和資料庫部署到生產 Web 應用程式和資料庫。 部署檔案和資料庫有許多項目可能出錯。 使用位置的**交換**功能，我們可以在升級期間減少停機時間，並降低部署變更時可能發生的失敗風險。
3. 可讓您能夠執行 **A / B 測試** 使用 [在生產環境中測試](http://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/) 功能

此範例將示範平台的彈性，讓您得以建置類似於 Umbraco Courier 模組的自訂模組，以便管理整個環境的部署。

## 參考

[使用 Azure App Service 的敏捷式軟體開發](app-service-agile-software-development)

[設定預備環境，在 Azure App Service web 應用程式](web-sites-staged-publishing)

[如何封鎖對非生產部署位置的 web 存取](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)





