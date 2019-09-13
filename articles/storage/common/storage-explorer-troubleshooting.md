---
title: Azure Storage Explorer hibaelhárítási útmutató | Microsoft Docs
description: A Azure Storage Explorer hibakeresési módszereinek áttekintése
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 69631b39403dedab56ed75cb145d464c0e1f747c
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935342"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer hibaelhárítási útmutató

A Microsoft Azure Storage Explorer egy önálló alkalmazás, amely lehetővé teszi az Azure Storage-alapú adattárolást Windows, macOS és Linux rendszeren. Az alkalmazás képes csatlakozni az Azure-ban, az országos felhőkben és a Azure Stack üzemeltetett Storage-fiókokhoz.

Ez az útmutató összefoglalja a Storage Explorerban látható gyakori problémák megoldásait.

## <a name="role-based-access-control-permission-issues"></a>Szerepköralapú Access Control engedélyekkel kapcsolatos problémák

A [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) lehetővé teszi az Azure-erőforrások részletes hozzáférés-kezelését azáltal, hogy az engedélyeket a _szerepkörökhöz_ötvözi. Az alábbiakban néhány olyan javaslatot talál, amelyekkel a RBAC Storage Explorer dolgozhat.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Mit kell látni a Storage Explorer erőforrásaim?

Ha problémákat tapasztal a tárolási erőforrások RBAC használatával történő elérésekor, annak oka az lehet, hogy a megfelelő szerepköröket nem rendelte hozzá. A következő szakaszok ismertetik azokat az engedélyeket, Storage Explorer jelenleg a tárolási erőforrások eléréséhez szükségesek.

Ha nem biztos benne, hogy rendelkezik a megfelelő szerepkörökkel vagy engedélyekkel, lépjen kapcsolatba az Azure-fiók rendszergazdájával.

#### <a name="read-listget-storage-accounts"></a>Olvasni Storage-fiók (ok) listázása/beolvasása

Rendelkeznie kell engedéllyel a Storage-fiókok listázásához. Ezt az engedélyt úgy érheti el, hogy az "olvasó" szerepkört rendeli hozzá.

#### <a name="list-storage-account-keys"></a>Tárfiókkulcsok listája

A Storage Explorer a kérelmek hitelesítéséhez is használhatja a fiók kulcsait. Nagyobb teljesítményű szerepkörökkel, például a közreműködő szerepkörrel férhet hozzá a kulcsokhoz.

> [!NOTE]
> A hozzáférési kulcsok nem korlátozott engedélyeket biztosítanak mindazoknak, akik tárolják azokat. Ezért általában nem ajánlott átadni a felhasználók fiókjának. Ha vissza kell vonnia a hozzáférési kulcsokat, újragenerálhatja őket az [Azure Portalról](https://portal.azure.com/).

#### <a name="data-roles"></a>Adatszerepkörök

Legalább egy olyan szerepkört hozzá kell rendelnie, amely hozzáférést biztosít az erőforrásokból származó olvasási adatokhoz. Ha például blobokat kell listáznia vagy letöltenie, akkor legalább a "Storage blob-Adatolvasó" szerepkörre lesz szüksége.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Miért van szükség felügyeleti rétegbeli szerepkörre az erőforrások Storage Explorer való megtekintéséhez?

Az Azure Storage két hozzáférési réteggel rendelkezik: a felügyelethez és _az_ _adatkezeléshez_ . Az előfizetések és a Storage-fiókok a felügyeleti rétegen keresztül érhetők el. A tárolók, a blobok és az egyéb adatforrások az adatrétegen keresztül érhetők el. Ha például szeretné lekérni a Storage-fiókjainak listáját az Azure-ból, küldjön egy kérelmet a felügyeleti végpontnak. Ha egy fiókban szeretné megtekinteni a blob-tárolók listáját, egy kérést küld a megfelelő szolgáltatási végpontnak.

A RBAC-szerepkörök a felügyeletre vagy az adatréteg-hozzáférésre vonatkozó engedélyeket is tartalmazhatnak. A "Reader" szerepkör például csak olvasási hozzáférés-vezérlési rétegbeli erőforrásokat biztosít.

Szigorúan véve az "olvasó" szerepkör nem biztosít adatrétegbeli engedélyeket, és nem szükséges az adatréteghez való hozzáféréshez.

A Storage Explorer megkönnyíti az erőforrások elérését az Azure-erőforrásokhoz való kapcsolódáshoz szükséges információk összegyűjtésével. A blob-tárolók megjelenítéséhez például Storage Explorer küld egy list containers-kérést a blob Service-végpontnak. A végpont beszerzéséhez Storage Explorer megkeresi azon előfizetések és Storage-fiókok listáját, amelyekhez hozzáférése van. Az előfizetések és a Storage-fiókok megkereséséhez azonban Storage Explorer is hozzá kell férnie a felügyeleti réteghez.

Ha nem rendelkezik felügyeleti rétegbeli engedélyekkel rendelkező szerepkörrel, Storage Explorer nem tudja lekérni az adatréteghez való kapcsolódáshoz szükséges adatokat.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Mi a teendő, ha nem tudom beolvasni a felügyeleti réteghez szükséges engedélyeket a rendszergazdától?

Jelenleg még nem áll rendelkezésre RBAC kapcsolatos megoldás. Megkerülő megoldásként igényelhet SAS URI-t az [erőforráshoz való csatoláshoz](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Hiba: Önaláírt tanúsítvány a tanúsítványláncot (és hasonló hibák)

A tanúsítvány hibáit a következő két helyzet egyike okozza:

1. Az alkalmazás egy "transzparens proxyn" keresztül csatlakozik, ami azt jelenti, hogy egy kiszolgáló (például a vállalati kiszolgáló) elfogja a HTTPS-forgalmat, visszafejti azt, majd egy önaláírt tanúsítvánnyal titkosítja.
2. Olyan alkalmazást futtat, amely az önaláírt SSL-tanúsítványokat beinjektálja a kapott HTTPS-üzenetbe. A tanúsítványokat beinjektáló alkalmazások például a víruskereső és a hálózati forgalom ellenőrzési szoftverét tartalmazzák.

Ha Storage Explorer egy önaláírt vagy nem megbízható tanúsítványt lát, akkor már nem tudja, hogy megváltozott-e a kapott HTTPS-üzenet. Ha az önaláírt tanúsítvány egy példányával rendelkezik, a következő lépések végrehajtásával utasíthatja Storage Explorer megbízhatóságát:

1. A tanúsítvány alap-64 kódolású X. 509 (. cer) másolatának beszerzése
2. Kattintson a **Szerkesztés** → **SSL-tanúsítványok** → **tanúsítványok importálása**lehetőségre, majd a file Picker használatával keresse meg, válassza ki és nyissa meg a. cer fájlt.

Ez a probléma több tanúsítvány (root és Intermediate) miatt is lehet. A hiba leküzdéséhez mindkét tanúsítványt hozzá kell adni.

Ha nem biztos abban, hogy honnan származik a tanúsítvány, a következő lépésekkel keresheti meg:

1. Telepítse az Open SSL-t.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (a fényváltozatok bármelyikének elegendőnek kell lennie)
    * Mac és Linux: az operációs rendszer részét képezi
2. Futtassa az Open SSL-t.
    * Windows: Nyissa meg a telepítési könyvtárat, kattintson a **/bin/** elemre, majd kattintson duplán az **OpenSSL. exe fájlra**.
    * Mac és Linux: az **OpenSSL** futtatása terminálról.
3. Hajtsa végre az `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha nem biztos abban, hogy mely tanúsítványok önaláírtak, keresse meg a tárgyat `("s:")` és a `("i:")` kiállítót.
5. Ha bármilyen önaláírt tanúsítványt talált, mindegyikhez másolja és illessze be a ( **-----a-----tanúsítvány megkezdése** és beolvasása, valamint az új. cer kiterjesztésű fájlba való **-----záró tanúsítvány-----** .
6. Nyissa meg Storage Explorer, kattintson az **Edit** → **SSL-tanúsítványok** → **tanúsítványok importálása**lehetőségre, majd a file Picker használatával keresse meg, válassza ki és nyissa meg a létrehozott. cer fájlokat.

Ha az előző lépésekkel nem talál önaláírt tanúsítványokat, további segítségért lépjen kapcsolatba velünk a visszajelzési eszköz segítségével. Azt is megteheti, hogy elindít Storage Explorer a parancssorból `--ignore-certificate-errors` a jelzővel. Ha ezzel a jelzővel indul el, Storage Explorer figyelmen kívül hagyja a tanúsítvány hibáit.

## <a name="sign-in-issues"></a>Bejelentkezési problémák

### <a name="blank-sign-in-dialog"></a>Üres bejelentkezési párbeszédpanel

Az üres bejelentkezési párbeszédpaneleket általában az ADFS okozza, hogy az Storage Explorer átirányítást hajt végre, amelyet az elektron nem támogat. A probléma megkerüléséhez próbálja meg használni a bejelentkezéshez használt eszköz kódjának folyamatát. Ehhez hajtsa végre a következő lépéseket:

1. Menüben Előzetes verzió – > "az eszköz kódjának használata bejelentkezés".
2. Nyissa meg a csatlakoztatás párbeszédpanelt (vagy a bal oldali függőleges sávban a dugó ikonra kattintva, vagy a fiók panel "fiók hozzáadása" elemét).
3. Válassza ki azt a környezetet, amelyre be szeretné jelentkezni.
4. Kattintson a "Bejelentkezés" gombra.
5. Kövesse a következő panelen megjelenő utasításokat.

Ha úgy találja, hogy a használni kívánt fiókba jelentkezik be, mert az alapértelmezett böngésző már be van jelentkezve egy másik fiókba, akkor a következőkre van lehetősége:

1. Manuálisan másolja a hivatkozást és a kódot a böngésző privát munkamenetére.
2. Manuálisan másolja a hivatkozást és a kódot egy másik böngészőbe.

### <a name="reauthentication-loop-or-upn-change"></a>Újrahitelesítési hurok vagy UPN-változás

Ha újrahitelesítési ciklust használ, vagy módosította valamelyik fiók UPN-azonosítóját, próbálkozzon a következő lépésekkel:

1. Távolítsa el az összes fiókot, majd zárjunk be Storage Explorer
2. Törölje a t. IdentityService mappát a számítógépről. Windows rendszeren a mappa a következő helyen található `C:\users\<username>\AppData\Local`:. Mac és Linux rendszereken a felhasználói könyvtár gyökerében található mappát találja.
3. Ha Mac vagy Linux rendszert használ, akkor törölnie kell a Microsoft. Developer. IdentityService bejegyzést az operációs rendszer-áruházból. Mac gépen a kulcstartó a "GNOME kulcstartó" alkalmazás. A Linux esetében az alkalmazás neve általában "kulcstartó", de a név a terjesztéstől függően eltérő lehet.

### <a name="conditional-access"></a>Feltételes hozzáférés

A feltételes hozzáférés nem támogatott, ha a Storage Explorer Windows 10-es, Linux vagy macOS rendszeren használatos. Ennek oka az Storage Explorer által használt HRE-könyvtár korlátozása.

## <a name="mac-keychain-errors"></a>Mac kulcstartó hibák

A macOS-kulcstartó időnként olyan állapotba kerülhet, amely a Storage Explorer hitelesítési könyvtárával kapcsolatos problémákat okoz. Ha le szeretné kérni a kulcstartót, próbálkozzon az alábbi lépésekkel:

1. Storage Explorer bezárásához.
2. Nyissa meg a kulcstartót (**cmd + szóköz**, írja be a kulcstartót, nyomja meg az ENTER billentyűt).
3. Válassza ki a "login" kulcstartót.
4. Kattintson a lakat ikonra a kulcstartó zárolásához (a lakat egy zárolt pozícióra animálva, ha elkészült, eltarthat néhány másodpercig), attól függően, hogy milyen alkalmazásokat nyitott meg.

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Storage Explorer elindítása.
6. Egy előugró ablaknak meg kell jelennie a következőhöz hasonló módon: "a Service hub szeretné elérni a kulcstartót". Ha igen, adja meg a Mac-rendszergazdai fiók jelszavát, és kattintson a **mindig engedélyezze** **(vagy az engedélyezés,** ha a **mindig engedélyezve** van) lehetőségre.
7. Próbáljon meg bejelentkezni.

### <a name="general-sign-in-troubleshooting-steps"></a>Általános bejelentkezési hibaelhárítási lépések

* Ha macOS-en van, és a bejelentkezési ablak soha nem jelenik meg a "hitelesítésre való várakozáskor". párbeszédpanelen, majd próbálja meg [ezeket a lépéseket](#mac-keychain-errors)
* Újraindítás Storage Explorer
* Ha a hitelesítési ablak üres, várjon legalább egy percet, mielőtt bezárja a hitelesítés párbeszédpanelt.
* Győződjön meg arról, hogy a proxy és a tanúsítvány beállításai megfelelően vannak konfigurálva a gép és a Storage Explorer számára.
* Ha Windows rendszert használ, és a Visual Studio 2019-et ugyanazon a gépen szeretné elérni, és bejelentkezik, próbáljon meg bejelentkezni a Visual Studio 2019-ba. Miután sikeresen bejelentkezett a Visual Studio 2019-be, megnyithatja Storage Explorer és megtekintheti a fiókját a fiók panelen.

Ha ezek közül egyik módszer sem működik, [a githubon nem kell megnyitnia a problémát](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Hiányzó előfizetések és megszakított bérlők

Ha a sikeres bejelentkezést követően nem tudja lekérni az előfizetéseket, próbálkozzon az alábbi hibaelhárítási módszerekkel:

* Ellenőrizze, hogy a fiókja rendelkezik-e hozzáféréssel a várt előfizetésekhez. Az Ön hozzáférését a portálon ellenőrizheti a használni kívánt Azure-környezethez való bejelentkezéssel.
* Győződjön meg arról, hogy a megfelelő Azure-környezettel (Azure, Azure China 21Vianet, Azure Germany, Azure USA Government vagy egyéni környezet) jelentkezett be.
* Ha proxy mögött van, ellenőrizze, hogy megfelelően konfigurálta-e a Storage Explorer proxyt.
* Próbálja meg eltávolítani és újból hozzáadni a fiókot.
* Ha van "További információ" hivatkozás, tekintse meg és tekintse meg, hogy a rendszer milyen hibaüzeneteket jelentett a sikertelen bérlők számára. Ha a you'ren't biztos abban, hogy mi a teendő a megjelenő hibaüzenetekkel, nyugodtan [Nyisson meg egy problémát a githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>A csatolt fiók vagy a tárolási erőforrás nem távolítható el

Ha nem tudja eltávolítani a csatolt fiókot vagy tárolási erőforrást a felhasználói felületen, manuálisan törölheti az összes csatolt erőforrást a következő mappák törlésével:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> A fenti mappák törlése előtt Storage Explorer bezárásához.

> [!NOTE]
> Ha valaha is importált SSL-tanúsítványokat, `certs` akkor készítsen biztonsági másolatot a könyvtár tartalmáról. Később a biztonsági mentés használatával újraimportálhatja az SSL-tanúsítványokat.

## <a name="proxy-issues"></a>Proxyval kapcsolatos problémák

Először győződjön meg arról, hogy a megadott adatok helyesek:

* A proxy URL-címe és portszáma
* Felhasználónév és jelszó, ha a proxy szükséges

> [!NOTE]
> A Storage Explorer nem támogatja a proxy automatikus konfigurációs fájljait a proxybeállítások konfigurálásához.

### <a name="common-solutions"></a>Gyakori megoldások

Ha továbbra is problémákat tapasztal, próbálkozzon az alábbi hibaelhárítási módszerekkel:

* Ha a proxy használata nélkül tud csatlakozni az internethez, győződjön meg arról, hogy a Storage Explorer a proxybeállítások engedélyezése nélkül működik. Ebben az esetben előfordulhat, hogy probléma van a proxy beállításaival. Működjön együtt a proxy rendszergazdájával a problémák azonosításához.
* Ellenőrizze, hogy a proxykiszolgálót használó más alkalmazások a várt módon működnek-e.
* Ellenőrizze, hogy tud-e csatlakozni a használni kívánt Azure-környezethez kapcsolódó portálhoz
* Ellenőrizze, hogy kaphat-e válaszokat a szolgáltatási végpontokról. Adja meg az egyik végpont URL-címét a böngészőben. Ha csatlakozni tud, egy InvalidQueryParameterValue vagy egy hasonló XML-választ kell kapnia.
* Ha valaki más is Storage Explorert használ a proxykiszolgálóhoz, ellenőrizze, hogy tud-e kapcsolatot létesíteni. Ha csatlakozni tudnak, előfordulhat, hogy kapcsolatba kell lépnie a proxykiszolgáló rendszergazdájával.

### <a name="tools-for-diagnosing-issues"></a>Problémák diagnosztizálására szolgáló eszközök

Ha van hálózati eszköze, például a Hegedűs a Windowshoz, akkor a következő módon diagnosztizálhatja a problémákat:

* Ha a proxyn keresztül kell dolgoznia, előfordulhat, hogy a hálózati eszközt úgy kell konfigurálnia, hogy a proxyn keresztül csatlakozhasson.
* Keresse meg a hálózati eszköz által használt portszámot.
* Adja meg a helyi gazdagép URL-címét és a hálózati eszköz portszámát a Storage Explorer proxybeállításait. Ha helyesen végzett, a hálózati eszköz elindítja a Storage Explorer által a felügyeleti és szolgáltatási végpontoknak küldött hálózati kérések naplózását. Adja meg https://cawablobgrs.blob.core.windows.net/ például a blob-végpontot egy böngészőben, és a következőhöz hasonló választ kap, amely azt sugallja, hogy az erőforrás létezik, de nem fér hozzá.

![Mintakód](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Kapcsolatfelvétel a proxykiszolgáló rendszergazdájával

Ha a proxybeállítások helyesek, előfordulhat, hogy kapcsolatba kell lépnie a proxykiszolgáló rendszergazdájával, és

* Győződjön meg arról, hogy a proxy nem blokkolja az Azure-felügyeletre vagy az erőforrás-végpontokra irányuló forgalmat.
* Ellenőrizze a proxykiszolgáló által használt hitelesítési protokollt. Storage Explorer jelenleg nem támogatja az NTLM-proxykat.

## <a name="unable-to-retrieve-children-error-message"></a>"Nem sikerült beolvasni a gyermekeket" hibaüzenet jelenik meg

Ha egy proxyn keresztül csatlakozik az Azure-hoz, ellenőrizze, hogy helyesek-e a proxybeállítások. Ha hozzáférést kap az erőforráshoz az előfizetés vagy a fiók tulajdonosa, ellenőrizze, hogy rendelkezik-e az adott erőforráshoz tartozó olvasási vagy listázási engedélyekkel.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>A kapcsolatok karakterlánca nem rendelkezik teljes konfigurációs beállításokkal

Ha ez a hibaüzenet jelenik meg, előfordulhat, hogy nem rendelkezik a szükséges engedélyekkel a Storage-fiók kulcsainak beszerzéséhez. Ha szeretné megerősíteni, hogy ez a helyzet, lépjen a portálra, és keresse meg a Storage-fiókját. Ezt gyorsan elvégezheti, ha a jobb gombbal rákattint a Storage-fiók csomópontjára, majd a Megnyitás a portálon lehetőségre kattint. Ha ezt megteszi, nyissa meg a "hozzáférési kulcsok" panelt. Ha nem rendelkezik a kulcsok megtekintéséhez szükséges engedélyekkel, megjelenik egy oldal, amelyen "nincs hozzáférése" üzenet jelenik meg. A probléma megkerüléséhez megszerezheti a fiók kulcsát valaki más nevével és kulcsával, vagy megkérheti az SAS-t a Storage-fiókhoz, és használhatja azt a Storage-fiók csatlakoztatásához.

Ha látja a fiók kulcsait, akkor a GitHubon a probléma megoldásához segítünk a probléma megoldásában.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Hiba történt az új kapcsolatok hozzáadásakor: TypeError: Nem definiált "version" tulajdonság nem olvasható

Ha ez a hibaüzenet akkor jelenik meg, amikor egyéni csatlakozást próbál hozzáadni, akkor előfordulhat, hogy a helyi hitelesítőadat-kezelőben tárolt kapcsolódási adatok sérültek.
A probléma megkerüléséhez megpróbálkozhat a sérült helyi kapcsolatok törlésével, majd azok újbóli hozzáadásával.

1. Storage Explorer elindítása. A felső menüben kattintson a Súgó → Fejlesztői eszközök váltása elemre.
2. A megnyitott ablakban válassza az alkalmazás lap → helyi tároló (bal oldali oldal) → file://
3. Attól függően, hogy milyen típusú kapcsolatokkal rendelkezik, keresse meg a kulcsát, és másolja az értékét egy szövegszerkesztőbe. Az érték az egyéni kapcsolatok neveinek egy tömbje.
    * Tárfiókok
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * BLOB-tárolók
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Fájlmegosztások
        * `StorageExplorer_CustomConnections_Files_v1`
    * Várólisták
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Táblák
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Az aktuális kapcsolatok nevének mentése után állítsa a Fejlesztői eszközök `[]`értéket a következőre:.

Ha meg szeretné őrizni a nem sérült kapcsolatokat, hajtsa végre a következő lépéseket a sérült kapcsolatok megkereséséhez. Ha nem bánja az összes meglévő kapcsolat elvesztésével, kihagyhatja a következő lépéseket, és a platformra vonatkozó utasításokat követve törölheti a kapcsolati adatait.

1. A szövegszerkesztőben adja meg újra az egyes kapcsolatok nevét a Fejlesztői eszközökhoz, és győződjön meg arról, hogy a kapcsolat továbbra is működik-e.
2. Ha a kapcsolat megfelelően működik, nem sérült, és nyugodtan elhagyhatja. Ha egy kapcsolat nem működik, távolítsa el az értékét a Fejlesztői eszközökból, és jegyezze fel, hogy később újra hozzá lehessen adni.
3. Ismételje meg az összes kapcsolat vizsgálatát.

Miután az összes kapcsolaton áthaladt, az összes nem visszaadott kapcsolat neve esetén törölje a sérült adatait (ha van ilyen), és adja hozzá őket a normál lépések végrehajtásához a Storage Explorer használatával.

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. A "Hitelesítőadat-kezelő" megnyitásához nyissa meg a Start menüt, és keressen rá a "Hitelesítőadat-kezelő" kifejezésre.
2. A megnyitott ablakban válassza a "Windows hitelesítő adatok" lehetőséget.
3. Az "általános hitelesítő adatok" területen keresse meg a `<connection_type_key>/<corrupted_connection_name>` Key ( `StorageExplorer_CustomConnections_Accounts_v1/account1`például) bejegyzést.
4. Távolítsa el ezeket a bejegyzéseket, és adja hozzá a kapcsolatokat vissza.

# <a name="macostabmacos"></a>[macOS](#tab/macOS)

1. Nyisson meg egy reflektorfényt (Command-szóköz), és keressen rá a "kulcstartó-hozzáférés" kifejezésre.
2. Keresse meg a kulcsot `<connection_type_key>/<corrupted_connection_name>` tartalmazó bejegyzéseket ( `StorageExplorer_CustomConnections_Accounts_v1/account1`például:).
3. Törölje ezeket a bejegyzéseket, és adja hozzá a kapcsolatokat vissza.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

A helyi hitelesítő adatok kezelése a Linux-disztribúciótól függően változhat. Ha a Linux-disztribúció nem biztosít beépített GUI-eszközt a helyi hitelesítő adatok kezeléséhez, telepíthet egy külső gyártótól származó eszközt a helyi hitelesítő adatok kezeléséhez. Használhatja például a [csikóhalat](https://wiki.gnome.org/Apps/Seahorse/), amely egy nyílt forráskódú GUI-eszköz a Linux helyi hitelesítő adatainak kezeléséhez.

1. Nyissa meg a helyi Hitelesítőadat-kezelő eszközt, és keresse meg a mentett hitelesítő adatokat.
2. Keresse meg a kulcsot `<connection_type_key>/<corrupted_connection_name>` tartalmazó bejegyzéseket ( `StorageExplorer_CustomConnections_Accounts_v1/account1`például:).
3. Törölje ezeket a bejegyzéseket, és adja hozzá a kapcsolatokat vissza.

Ha a fenti lépések elvégzése után is ezt a hibát tapasztalja, vagy ha meg szeretné osztani, hogy mit gondol a kapcsolatok, [Nyisson meg egy problémát](https://github.com/microsoft/AzureStorageExplorer/issues) a GitHub oldalán.

## <a name="issues-with-sas-url"></a>Az SAS URL-címmel kapcsolatos problémák

Ha SAS URL-cím használatával csatlakozik egy szolgáltatáshoz, és a következő hibát tapasztalja:

* Ellenőrizze, hogy az URL-cím tartalmazza-e a szükséges engedélyeket az erőforrások olvasásához vagy listázásához.
* Ellenőrizze, hogy az URL-cím nem járt-e le.
* Ha a SAS URL-cím egy hozzáférési házirenden alapul, ellenőrizze, hogy a hozzáférési házirendet nem vonták-e vissza.

Ha egy érvénytelen SAS URL-cím használatával véletlenül csatolta, és nem tud leválasztani, kövesse az alábbi lépéseket:

1. Storage Explorer futtatásakor nyomja meg az F12 billentyűt a fejlesztői eszközök ablak megnyitásához.
2. Kattintson az alkalmazás lapra, majd a bal oldali fában a helyi tároló > file://elemre.
3. Keresse meg a problémás SAS URI-azonosító szolgáltatás típusához társított kulcsot. Ha például a hibás SAS URI egy blob-tárolóhoz tartozik, keresse meg a nevű `StorageExplorer_AddStorageServiceSAS_v1_blob`kulcsot.
4. A kulcs értékének JSON-tömbnek kell lennie. Keresse meg a rossz URI-hoz társított objektumot, és távolítsa el.
5. Nyomja le a CTRL + R billentyűkombinációt Storage Explorer újratöltéséhez.

## <a name="linux-dependencies"></a>Linux-függőségek

Storage Explorer 1.10.0 és újabb verzió is elérhető a Snap áruházban. A Storage Explorer beépülő modul automatikusan telepíti az összes függőségét, és frissíti, ha a beépülő modul új verziója érhető el. Az Storage Explorer beépülő modul telepítése a javasolt telepítési módszer.

Storage Explorer a Password Manager használatát igényli, amelyet manuálisan kell csatlakoztatni, mielőtt Storage Explorer megfelelően működni fognak. A következő paranccsal csatlakozhat Storage Explorer a rendszer jelszavas kezelőjéhez:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Az alkalmazást. tar. gz fájlként is letöltheti, de a függőségeket manuálisan kell telepítenie.

> [!IMPORTANT]
> A. tar. gz letöltésben megadott Storage Explorer csak az Ubuntu-disztribúciók esetében támogatott. Más terjesztések nem lettek ellenőrizve, és alternatív vagy további csomagokat igényelhetnek.

Ezek a csomagok a leggyakoribb követelmények a Linux Storage Explorer esetén:

* [.NET Core 2,0 futtatókörnyezet](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` vagy `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Storage Explorer 1.7.0 és korábbi verziókhoz a .NET Core 2,0 szükséges. Ha telepítve van a .NET Core újabb verziója, akkor a [Storage Explorerra](#patching-storage-explorer-for-newer-versions-of-net-core)kell frissítenie. Ha Storage Explorer 1.8.0 vagy újabb rendszert futtat, akkor akár a .NET Core 2,2-as verzióját is használhatja. Az 2,2-nál újabb verziók nem lettek ellenőrizve a működéshez.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19,04](#tab/1904)

1. Storage Explorer letöltése.
2. Telepítse a [.net Core futtatókörnyezetet](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Futtassa a következő parancsot:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18,04](#tab/1804)

1. Storage Explorer letöltése.
2. Telepítse a [.net Core futtatókörnyezetet](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Futtassa a következő parancsot:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16,04](#tab/1604)

1. Storage Explorer letöltése
2. Telepítse a [.net Core futtatókörnyezetet](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Futtassa a következő parancsot:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14,04](#tab/1404)

1. Storage Explorer letöltése
2. Telepítse a [.net Core futtatókörnyezetet](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Futtassa a következő parancsot:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Javítás Storage Explorer a .NET Core újabb verzióihoz

Előfordulhat, hogy a Storage Explorer 1.7.0 vagy régebbi verziójának a Storage Explorer által használt .NET Core-verziót kell megjavítania.

1. Töltse le a StreamJsonRpc 1.5.43 verzióját a [nuget-](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)ból. Keresse meg a "csomag letöltése" hivatkozást az oldal jobb oldalán.
2. A csomag letöltése után módosítsa a fájl kiterjesztését `.nupkg` a verzióról a `.zip`verzióra.
3. Bontsa ki a csomagot.
4. Nyissa meg a `streamjsonrpc.1.5.43/lib/netstandard1.1/` mappát.
5. Másolja `StreamJsonRpc.dll` a következő helyszínekre a Storage Explorer mappában:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>A Azure Portal nem működik az Explorerben.

Ha a Azure Portal "Megnyitás a Explorerben" gombja nem működik, győződjön meg róla, hogy kompatibilis böngészőt használ. A következő böngészők lettek tesztelve a kompatibilitás érdekében.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>További lépések

Ha egyik megoldás sem működik Önnek, [Nyisson meg egy problémát a githubon](https://github.com/Microsoft/AzureStorageExplorer/issues). Gyorsan elérheti a GitHubot a bal alsó sarokban található "jelentés a GitHub használatával" gombra kattintva.

![Visszajelzés](./media/storage-explorer-troubleshooting/feedback-button.PNG)
