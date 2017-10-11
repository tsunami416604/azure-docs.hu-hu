## <a name="install-wordpress"></a>WordPress telepítése

Ha ki szeretné próbálni a Jegyzettömböt, a minta-alkalmazások telepítése. Tegyük fel, a következő lépéseket a nyílt forráskódú telepítése [WordPress](https://wordpress.org/) platform webhelyek és blogok létrehozásához. Más munkaterhelésekhez, próbálja meg a következők [Drupal](http://www.drupal.org) és [Moodle](https://moodle.org/). 

A WordPress-telepítő a koncepció igazolása szolgál. További információk és éles telepítési beállításait, a [WordPress dokumentáció](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>A WordPress telepítéséhez

Futtassa az alábbi parancsot:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>A WordPress konfigurálása

WordPress MySQL és a PHP használatára konfigurálja. Nyisson meg egy szövegszerkesztőt, az Ön által választott, és a fájl létrehozásához a következő parancsot `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Másolja a következő sorokat a fájlt, és az adatbázis-jelszót *yourPassword* (hagyja változatlanul más értékek). Ezután mentse a fájlt.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```

Egy munkakönyvtárba, hozzon létre egy szövegfájlt `wordpress.sql` a WordPress-adatbázis konfigurálása: 

```bash
sudo sensible-editor wordpress.sql
```

Adja hozzá a következő parancsokat, és az adatbázis-jelszót *yourPassword* (hagyja változatlanul más értékek). Ezután mentse a fájlt.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```


A következő parancsot az adatbázis létrehozásához:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

A parancs befejezése után törölje a fájlt `wordpress.sql`.

Helyezze át a WordPress telepítése a web server dokumentumgyökér:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Most már a WordPress beállításának befejezése és közzététele a platformon. Nyisson meg egy böngészőt, és navigáljon `http://yourPublicIPAddress/wordpress`. Helyettesítse be a virtuális gép nyilvános IP-címét. Ez a rendszerkép hasonlóan kell kinéznie.

![WordPress telepítése oldal](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)