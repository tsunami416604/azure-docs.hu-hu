## <a name="prepare-your-intel-nuc"></a>Az Intel NUC előkészítése

A hardver-telepítés befejezéséhez kell:

- Az Intel NUC csatlakozni a csomagban szereplő tápegység.
- Az Intel NUC csatlakoznak a hálózathoz, az Ethernet-kábelen keresztül.

Ezzel befejezte az Intel NUC átjáróeszköz hardver beállítása.

### <a name="sign-in-and-access-the-terminal"></a>Bejelentkezhet és elérheti a Terminálszolgáltatások

A Terminálszolgáltatások tesztkörnyezetben, a Intel NUC eléréséhez két lehetőség közül választhat:

- Ha a billentyűzetet és a figyelő az Intel NUC csatlakozik, a rendszerhéj közvetlenül is elérheti. Az alapértelmezett hitelesítő adatok felhasználónév **legfelső szintű** és a jelszó **legfelső szintű**.

- Az SSH használata a asztali gépen Intel NUC a rendszerhéj a hozzáférést.

#### <a name="sign-in-with-ssh"></a>SSH bejelentkezés

SSH bejelentkezni kell az Intel NUC IP-címét. Ha a figyelő az Intel NUC csatlakoztatott és a billentyűzeten, használja a `ifconfig` parancs az IP-cím kereséséhez. Azt is megteheti csatlakoztassa a útválasztót, hogy a címek a hálózati eszközök listában.

Jelentkezzen be a felhasználónevet **legfelső szintű** és a jelszó **legfelső szintű**.

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>Választható lehetőség: Az Intel NUC a mappa megosztása

Ha szükséges érdemes lehet az Intel NUC a mappa megosztása az asztali környezetet. A mappa megosztása lehetővé teszi az előnyben részesített asztali szövegszerkesztővel (például [Visual Studio Code](https://code.visualstudio.com/) vagy [Sublime Text](http://www.sublimetext.com/)) az Intel NUC használata helyett a fájlok szerkesztésére `nano` vagy `vi`.

A Windows mappa megosztásához a Intel NUC Samba kiszolgáló konfigurálása. Másik megoldásként használhatja a SFTP-kiszolgáló az Intel NUC egy SFTP-ügyféllel a asztali számítógépre a.
