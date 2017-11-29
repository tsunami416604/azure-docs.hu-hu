## <a name="install-wordpress"></a>WordPress telepítése

Ha ki szeretné próbálni a Jegyzettömböt, a minta-alkalmazások telepítése. Tegyük fel, a következő lépéseket a nyílt forráskódú telepítése [WordPress](https://wordpress.org/) platform webhelyek és blogok létrehozásához. Más munkaterhelésekhez, próbálja meg a következők [Drupal](http://www.drupal.org) és [Moodle](https://moodle.org/). 

A WordPress-telepítő csak a koncepció igazolása szolgál. A legújabb WordPress telepítése éles ajánlott biztonsági beállításokkal, tekintse meg a [WordPress dokumentáció](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>A WordPress telepítéséhez

Futtassa az alábbi parancsot:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>A WordPress konfigurálása

WordPress MySQL és a PHP használatára konfigurálja.

Egy munkakönyvtárba, hozzon létre egy szövegfájlt `wordpress.sql` WordPress a MySQL-adatbázis konfigurálása: 

```bash
sudo sensible-editor wordpress.sql
```

Adja hozzá a következő parancsokat, és az Ön által választott, az adatbázis jelszó *yourPassword* (hagyja változatlanul más értékek). Ha korábban állít be egy MySQL-biztonsági házirend ellenőrzése a jelszó erőssége, győződjön meg arról, hogy a jelszó erőssége megfelel. Mentse a fájlt.

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

Mivel a fájl `wordpress.sql` tartalmazza az adatbázis hitelesítő adatai, a törlés után használja:

```bash
sudo rm wordpress.sql
```

A PHP konfigurálásához futtassa a következő parancsot, nyisson meg egy szövegszerkesztőt, az Ön által választott, és a fájl létrehozásához `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Másolja a következő sorokat a fájlt, és a WordPress adatbázis jelszó *yourPassword* (hagyja változatlanul más értékek). Ezután mentse a fájlt.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Helyezze át a WordPress telepítése a web server dokumentumgyökér:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Most már a WordPress beállításának befejezése és közzététele a platformon. Nyisson meg egy böngészőt, és navigáljon `http://yourPublicIPAddress/wordpress`. Helyettesítse be a virtuális gép nyilvános IP-címét. Ez a rendszerkép hasonlóan kell kinéznie.

![WordPress telepítése oldal](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)