---
title: Az Azure Storage Explorer hibaelhárítási útmutatója | Microsoft dokumentumok
description: Az Azure Storage Explorer hibakeresési technikáinak áttekintése
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: db36033ea524603416f16db27f40d5eefb8bf613
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437115"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Az Azure Storage Explorer hibaelhárítási útmutatója

A Microsoft Azure Storage Explorer egy önálló alkalmazás, amely megkönnyíti az Azure Storage-adatok windowsos, macOS és Linux rendszeren történő használatát. Az alkalmazás csatlakozhat az Azure-ban, a nemzeti felhőkön és az Azure Stacken tárolt tárfiókokhoz.

Ez az útmutató a Storage Explorerben gyakran előforduló problémák megoldásait foglalja össze.

## <a name="rbac-permissions-issues"></a>RBAC-engedélyekkel kapcsolatos problémák

A szerepköralapú hozzáférés-vezérlés [RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) lehetővé teszi az Azure-erőforrások nagy részletességű hozzáférés-kezelését az engedélyek _szerepkörökbe_való egyesítésével. Íme néhány stratégia az RBAC optimális működéséhez a Storage Explorerben.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Hogyan érhetem el az erőforrásaimat a Storage Explorerben?

Ha problémái vannak a tárolási erőforrások rbac-on keresztüli elérésével, előfordulhat, hogy nem kapta hozzá a megfelelő szerepköröket. A következő szakaszok ismertetik azokat az engedélyeket, amelyeket a Storage Explorer jelenleg a tárolási erőforrásokhoz való hozzáféréshez igényel. Forduljon az Azure-fiók rendszergazdájához, ha nem biztos abban, hogy rendelkezik a megfelelő szerepkörökkel vagy engedélyekkel.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Olvassa el: Lista/Tárfiók(ok) beolvasása" engedélyekkel kapcsolatos probléma

A tárfiókok listázásához engedéllyel kell rendelkeznie. Az engedély beolvasásához hozzá kell rendelnie az _Olvasó_ szerepkört.

#### <a name="list-storage-account-keys"></a>Tárfiók kulcsainak listázása

A Storage Explorer fiókkulcsok használatával is hitelesítheti a kérelmeket. A fiókkulcsok hatékonyabb szerepkörökkel, például a _közreműködői_ szerepkörrel érhetők el.

> [!NOTE]
> A hozzáférési kulcsok korlátlan engedélyeket adnak mindenkinek, aki rendelkezik velük. Ezért nem javasoljuk, hogy adja ki ezeket a kulcsokat a fiók felhasználók számára. Ha vissza kell vonnia a hozzáférési kulcsokat, újragenerálhatja őket az [Azure Portalon.](https://portal.azure.com/)

#### <a name="data-roles"></a>Adatszerepkörök

Legalább egy szerepkörhöz hozzá kell rendelnie, amely hozzáférést biztosít az erőforrásokból származó adatok olvasásához. Ha például fel szeretné sorolni vagy le szeretné tölteni a blobokat, legalább a _Storage Blob Data Reader_ szerepkörre szüksége lesz.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Miért van szükségem felügyeleti rétegszerepkörre az erőforrások tárolása a Storage Explorerben való megtekintéséhez?

Az Azure Storage két hozzáférési réteggel rendelkezik: _a felügyelettel_ és _az adatokkal._ Az előfizetések és a tárfiókok a felügyeleti rétegen keresztül érhetők el. A tárolók, a blobok és az egyéb adaterőforrások az adatrétegen keresztül érhetők el. Például ha azt szeretné, hogy az Azure-ból a tárfiókok listáját, küldjön egy kérést a felügyeleti végpont. Ha azt szeretné, hogy egy fiókblob-tárolók listáját, küldjön egy kérelmet a megfelelő szolgáltatás végpontra.

Az RBAC-szerepkörök kezelési vagy adatréteg-hozzáféréshez szükséges engedélyeket tartalmazhatnak. Az Olvasó szerepkör például csak olvasható hozzáférést biztosít a felügyeleti réteg erőforrásaihoz.

Szigorúan véve az Olvasó szerepkör nem biztosít adatréteg-engedélyeket, és nem szükséges az adatréteg eléréséhez.

A Storage Explorer megkönnyíti az erőforrások elérését az Azure-erőforrásokhoz való csatlakozáshoz szükséges információk összegyűjtésével. Például a blob tárolók megjelenítéséhez a Storage Explorer egy "listatárolók" kérést küld a blob szolgáltatás végpontjának. A végpont lereményének lereményeléséhez a Storage Explorer megkeresi az előfizetések és a tárfiókok listáját, amelyekhez hozzáféréssel rendelkezik. Az előfizetések és a tárfiókok megkereséséhez a Storage Explorernek is hozzá kell férnie a felügyeleti réteghez.

Ha nem rendelkezik olyan szerepkörrel, amely felügyeleti réteghez nem biztosít engedélyt, a Storage Explorer nem tudja megszerezni az adatréteghez való csatlakozáshoz szükséges információkat.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Mi a teendő, ha nem tudom megszerezni a rendszergazdatől a felügyeleti réteg engedélyeit?

Jelenleg nincs RBAC-hoz kapcsolódó megoldás erre a kérdésre. Kerülő megoldásként kérhet egy SAS-URI-t [az erőforráshoz való csatoláshoz.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri)

### <a name="recommended-built-in-rbac-roles"></a>Ajánlott beépített RBAC-szerepkörök

Számos beépített RBAC szerepkör létezik, amelyek biztosíthatják a Storage Explorer használatához szükséges engedélyeket. Néhány ilyen szerepek a következők:
- [Tulajdonos](/azure/role-based-access-control/built-in-roles#owner): Mindent kezelhet, beleértve az erőforrásokhoz való hozzáférést is. **Megjegyzés:** ez a szerepkör hozzáférést biztosít a kulcshoz.
- [Közreműködő](/azure/role-based-access-control/built-in-roles#contributor): Az erőforrásokhoz való hozzáférés kivételével mindent kezelhet. **Megjegyzés:** ez a szerepkör hozzáférést biztosít a kulcshoz.
- [Olvasó](/azure/role-based-access-control/built-in-roles#reader): Az erőforrások olvasása és listázása.
- [Tárfiók közreműködője:](/azure/role-based-access-control/built-in-roles#storage-account-contributor)a tárfiókok teljes kezelése. **Megjegyzés:** ez a szerepkör hozzáférést biztosít a kulcshoz.
- [Storage Blob-adatok tulajdonosa:](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)Teljes hozzáférés az Azure Storage blob tárolók és adatok.
- [Storage Blob Data Közreműködő:](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)Az Azure Storage-tárolók és -blobok olvasása, írása és törlése.
- [Storage Blob Data Reader:](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)Olvassa el és sorolja fel az Azure Storage-tárolók és blobok.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Hiba: Önaláírt tanúsítvány a tanúsítványláncban (és hasonló hibák)

A tanúsítványhibák általában az alábbi esetek egyikében fordulnak elő:

- Az alkalmazás egy _transzparens proxyn_keresztül csatlakozik , ami azt jelenti, hogy egy kiszolgáló (például a vállalati kiszolgáló) elfogja a HTTPS-forgalmat, visszafejti, majd önaláírt tanúsítvánnyal titkosítja azt.
- Olyan alkalmazást futtat, amely önaláírt TLS/SSL-tanúsítványt ad a kapott HTTPS-üzenetekbe. Tanúsítványokat befecskendező alkalmazások közé tartozik például a víruskereső és a hálózati forgalom ellenőrző szoftvere.

Amikor a Storage Explorer önaláírt vagy nem megbízható tanúsítványt lát, már nem tudja, hogy a fogadott HTTPS-üzenet módosult-e. Ha rendelkezik az önaláírt tanúsítvány másolatával, az alábbi lépésekkel utasíthatja a Storage Explorert, hogy bízzon benne:

1. Szerezze be a tanúsítvány Base-64 kódolású X.509 (.cer) példányát.
2. Nyissa meg az**SSL-tanúsítványok** > importálásának **szerkesztése** > **tanúsítványokat,** majd a fájlválasztóval keresse meg, jelölje ki és nyissa meg a .cer fájlt.

Ez a probléma akkor is előfordulhat, ha több tanúsítvány (gyökér és köztes). A hiba megoldásához mindkét tanúsítványt hozzá kell adni.

Ha nem biztos abban, hogy honnan származik a tanúsítvány, az alábbi lépésekkel megkeresheti:

1. Telepítse az OpenSSL-t.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): A könnyű változatok bármelyikének elegendőnek kell lennie.
    * Mac és Linux: Szerepelnie kell az operációs rendszer.
2. Futtassa az OpenSSL-t.
    * Windows: Nyissa meg a telepítési könyvtárat, válassza a **/bin/** lehetőséget, majd kattintson duplán **az openssl.exe fájlra.**
    * Mac és Linux: Terminálról futva. `openssl`
3. Futtassa az `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha nem biztos abban, hogy mely tanúsítványok vannak önaláírtak, jegyezze fel bárhol, ahol a tulajdonos `("s:")` és a kibocsátó `("i:")` ugyanaz.
5. Ha önaláírt tanúsítványokat talál, másolja és illessze be a `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` fájlokat (beleértve) egy új .cer fájlba.
6. Nyissa meg a Tárolókezelőt, és nyissa meg az > **SSL-tanúsítványok** > **importálási tanúsítványainak szerkesztése című területet.** **Edit** Ezután a fájlválasztóval megkeresheti, kijelölheti és megnyithatja a létrehozott .cer fájlokat.

Ha az alábbi lépések végrehajtásával nem talál önaláírt tanúsítványokat, vegye fel velünk a kapcsolatot a visszajelzési eszköz segítségével. A Tárolókezelőt a parancssorból is `--ignore-certificate-errors` megnyithatja a jelző használatával. Ha ezzel a jelzővel nyitja meg, a Storage Explorer figyelmen kívül hagyja a tanúsítványhibákat.

## <a name="sign-in-issues"></a>Bejelentkezési problémák

### <a name="blank-sign-in-dialog-box"></a>Üres bejelentkezés párbeszédpanel

Az üres bejelentkezési párbeszédpanelek leggyakrabban akkor fordulnak elő, amikor az Active Directory összevonási szolgáltatások (AD FS) a Storage Explorer t kéri az Electron által nem támogatott átirányítás végrehajtására. A probléma kerülő megoldásához próbálja meg használni az Eszközkód-áramlást a bejelentkezéshez. Ehhez kövesse az alábbi lépéseket:

1. A bal oldali függőleges eszköztáron nyissa meg a **Beállítások lehetőséget.** A Beállítások panelen nyissa meg az **Alkalmazásbejelentkezés** > **lehetőséget.** **Eszközkód-bejelentkezés használata**engedélyezése.
2. Nyissa **meg** a Csatlakozás párbeszédpanelt (vagy a bal oldali függőleges sávon lévő dugóikon, vagy a **fiókpanelfiók hozzáadása** elemével) lehetőséget választja.
3. Válassza ki azt a környezetet, amelybe be szeretne jelentkezni.
4. Válassza **a Bejelentkezés**lehetőséget.
5. Kövesse a következő panelen található utasításokat.

Ha nem tud bejelentkezni a használni kívánt fiókba, mert az alapértelmezett böngésző már be van jelentkezve egy másik fiókba, tegye az alábbiak egyikét:

- A hivatkozást és a kódot manuálisan másolja a böngésző privát munkamenetébe.
- A hivatkozást és a kódot manuálisan másolja egy másik böngészőbe.

### <a name="reauthentication-loop-or-upn-change"></a>Újrahitelesítési ciklus vagy upn-módosítás

Ha újrahitelesítési ciklusban van, vagy megváltoztatta az egyik fiókja ötévenkénti azonosítóját, kövesse az alábbi lépéseket:

1. Távolítsa el az összes fiókot, majd zárja be a Tárterület-kezelőt.
2. Törölje a t. IdentityService mappát a számítógépről. Windows rendszerben a mappa `C:\users\<username>\AppData\Local`a helyen található. Mac és Linux esetén a mappa a felhasználói könyvtár gyökerében található.
3. Ha Mac vagy Linux rendszert használ, törölnie kell a Microsoft.Developer.IdentityService bejegyzést az operációs rendszer kulcstárolójából is. A Mac, a keystore a *Gnome Kulcstartó* alkalmazás. Linux alatt az alkalmazást általában _kulcstartónak_nevezik, de a név a disztribúciótól függően eltérő lehet.

### <a name="conditional-access"></a>Feltételes hozzáférés

A Storage Explorer által használt Azure AD-tár korlátozása miatt a feltételes hozzáférés nem támogatott, ha a Storage Explorert Windows 10, Linux vagy macOS rendszeren használják.

## <a name="mac-keychain-errors"></a>Mac-kulcskarikával kapcsolatos hibák

A macOS-kulcskarika néha olyan állapotba léphet, amely problémákat okoz a Storage Explorer hitelesítési könyvtárában. Ha ki szeretné hozni a kulcstartót ebből az állapotból, kövesse az alábbi lépéseket:

1. Zárja be a Tároló kezelőt.
2. Nyissa meg a billentyűt (nyomja le a Command+Szóköz billentyűkombinációt, írja be **a billentyűt,** és nyomja le az Enter billentyűt).
3. Válassza ki a "bejelentkezési" kulcskarikát.
4. A kulcskarika zárolásához válassza a lakat ikont. (A lakat a folyamat befejeztére kerüléskor zárolva jelenik meg. A megnyitott alkalmazásoktól függően eltarthat néhány másodpercig).

    ![Lakat ikon](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Nyissa meg a Storage Explorert.
6. A rendszer egy olyan üzenetet küld, hogy "A szolgáltatásközpont hozzá szeretne férni a kulcskarikához". Adja meg a Mac rendszergazdai fiók jelszavát, és válassza **a Mindig engedélyezés** (vagy **az Engedélyezés lehetőséget,** ha **a Mindig engedélyezés** nem érhető el).
7. Próbáljon meg bejelentkezni.

### <a name="general-sign-in-troubleshooting-steps"></a>Általános bejelentkezési hibaelhárítási lépések

* Ha macOS rendszert beszél, és a bejelentkezési ablak soha nem jelenik meg a Várakozás a **hitelesítésre** párbeszédpanelen, próbálkozzon [az alábbi lépésekkel.](#mac-keychain-errors)
* Indítsa újra a Tárolókezelőt.
* Ha a hitelesítési ablak üres, várjon legalább egy percet a hitelesítési párbeszédpanel bezárása előtt.
* Győződjön meg arról, hogy a proxy- és tanúsítványbeállítások megfelelően vannak konfigurálva mind a számítógéphez, mind a Tárolókezelőhöz.
* Ha Windows rendszert futtat, és ugyanazon a gépen van hozzáférése a Visual Studio 2019-hez és a bejelentkezési hitelesítő adatokhoz, próbáljon meg bejelentkezni a Visual Studio 2019-be. A Visual Studio 2019-be való sikeres bejelentkezés után megnyithatja a Storage Explorer alkalmazást, és megtekintheti a fiókját a fiókpanelen.

Ha a módszerek egyike sem működik, [nyisson meg egy problémát a GitHubon.](https://github.com/Microsoft/AzureStorageExplorer/issues)

### <a name="missing-subscriptions-and-broken-tenants"></a>Hiányzó előfizetések és hibás bérlők

Ha a sikeres bejelentkezés után nem tudja beolvasni az előfizetéseket, próbálkozzon az alábbi hibaelhárítási módszerekkel:

* Ellenőrizze, hogy fiókja hozzáfér-e a várt előfizetésekhez. A hozzáférést úgy ellenőrizheti, hogy bejelentkezik a portálra a használni kívánt Azure-környezethez.
* Győződjön meg arról, hogy a megfelelő Azure-környezetben (Azure, Azure China 21Vianet, Azure Germany, Azure US Government vagy Egyéni környezet) keresztül jelentkezett be.
* Ha proxykiszolgáló mögött van, ellenőrizze, hogy megfelelően konfigurálta-e a Storage Explorer proxyt.
* Próbálja meg eltávolítani, majd újra hozzáadni a fiókot.
* Ha van egy "További információ" hivatkozás, ellenőrizze, hogy mely hibaüzenetek vannak jelentve a bérlők, amelyek nem. Ha nem biztos abban, hogyan kell válaszolni a hibaüzenetekre, nyugodtan [nyisson meg egy problémát a GitHubon.](https://github.com/Microsoft/AzureStorageExplorer/issues)

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Nem lehet eltávolítani egy csatolt fiókot vagy tárolóerőforrást

Ha nem tud eltávolítani egy csatolt fiókot vagy tárolóerőforrást a felhasználói felületen keresztül, manuálisan törölheti az összes csatolt erőforrást a következő mappák törlésével:

* Windows:`%AppData%/StorageExplorer`
* Macos:`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux:`~/.config/StorageExplorer`

> [!NOTE]
> A mappák törlése előtt zárja be a Tárolókezelőt.

> [!NOTE]
> Ha valaha importált SSL-tanúsítványokat, biztonsági `certs` másolatot kell kapnia a könyvtár tartalmáról. Később a biztonsági másolat segítségével újraimportálhatja az SSL-tanúsítványokat.

## <a name="proxy-issues"></a>Proxyproblémák

Először győződjön meg arról, hogy a megadott alábbi adatok helyesek:

* A proxy URL-címe és a portszáma
* Felhasználónév és jelszó, ha a proxy megköveteli őket

> [!NOTE]
> A Storage Explorer nem támogatja az automatikus proxykonfigurációs fájlokat a proxybeállítások konfigurálásához.

### <a name="common-solutions"></a>Közös megoldások

Ha továbbra is problémákat tapasztal, próbálkozzon az alábbi hibaelhárítási módszerekkel:

* Ha a proxy használata nélkül is tud csatlakozni az internethez, ellenőrizze, hogy a Storage Explorer proxybeállítások nélkül működik-e. Ebben az esetben lehet probléma a proxybeállításokkal. A problémák azonosításához működjön együtt a rendszergazdával.
* Ellenőrizze, hogy a proxykiszolgálót használó többi alkalmazás a várt módon működik-e.
* Ellenőrizze, hogy tud-e csatlakozni a portálhoz a használni kívánt Azure-környezethez.
* Ellenőrizze, hogy fogadhat-e válaszokat a szolgáltatás végpontjairól. Adja meg az egyik végpont URL-jét a böngészőben. Ha tud csatlakozni, akkor invalidQueryParameterValue értéket vagy hasonló XML-választ kell kapnia.
* Ha valaki más is használja a Tárolókezelőt a proxykiszolgálón, ellenőrizze, hogy tud-e csatlakozni. Ha tudják, akkor lehet, hogy forduljon a proxy szerver admin.

### <a name="tools-for-diagnosing-issues"></a>Eszközök a problémák diagnosztizálására

Ha rendelkezik hálózati eszközökkel, például a Fiddler for Windows rendszerlel, a problémákat az alábbiak szerint diagnosztizálhatja:

* Ha a proxyn keresztül kell dolgoznia, előfordulhat, hogy konfigurálnia kell a hálózati eszközt a proxyn keresztüli csatlakozáshoz.
* Ellenőrizze a hálózati eszköz által használt portszámot.
* Adja meg a helyi állomás URL-címét és a hálózati eszköz portszámát proxybeállításként a Tárolókezelőben. Ha ezt helyesen teszi, a hálózati eszköz megkezdi a Storage Explorer által a felügyeleti és szolgáltatásvégpontok számára benyújtott hálózati kérelmek naplózását. Írja be `https://cawablobgrs.blob.core.windows.net/` például a blobvégpontot egy böngészőben, és a következőhöz hasonló választ kap:

  ![Kódminta](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Ez a válasz azt sugallja, hogy az erőforrás létezik, annak ellenére, hogy nem tudja elérni.

### <a name="contact-proxy-server-admin"></a>Kapcsolatfelvétel a proxykiszolgáló rendszergazdájával

Ha a proxybeállítások helyesek, előfordulhat, hogy a következő kapcsolatokkal kell felvennie a kapcsolatot a proxykiszolgáló rendszergazdájával:

* Győződjön meg arról, hogy a proxy nem blokkolja a forgalmat az Azure-kezelés vagy az erőforrás-végpontok.
* Ellenőrizze a proxykiszolgáló által használt hitelesítési protokollt. A Storage Explorer jelenleg nem támogatja az NTLM proxykat.

## <a name="unable-to-retrieve-children-error-message"></a>"Nem lehet beolvasni a gyermekeket" hibaüzenet jelenik meg

Ha egy proxyn keresztül csatlakozik az Azure-hoz, ellenőrizze, hogy a proxybeállításai helyesek-e. Ha az előfizetés vagy fiók tulajdonosától kap hozzáférést egy erőforráshoz, ellenőrizze, hogy rendelkezik-e olvasott vagy listázott engedélyekkel az adott erőforráshoz.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>A kapcsolati karakterlánc nem rendelkezik teljes konfigurációs beállításokkal

Ha ez a hibaüzenet jelenik meg, lehetséges, hogy nem rendelkezik a tárfiók kulcsainak beszerzéséhez szükséges engedélyekkel. Annak ellenőrzéséhez, hogy ez a helyzet, nyissa meg a portálon, és keresse meg a tárfiókot. Ezt úgy teheti meg, hogy a jobb gombbal a tárfiók csomópontjára kattint, és a **Megnyitás a portálon**parancsot választja. Ezután lépjen az **Access Keys** panelre. Ha nincs engedélye a kulcsok megtekintéséhez, megjelenik egy "Nincs hozzáférése" üzenet. A probléma kerülő megoldásához szerezze be a fiókkulcsot valaki mástól, és csatolja a név és a kulcs, vagy kérhet valaki egy SAS a tárfiókba, és használja a tárfiók csatolásához.

Ha látja a fiókkulcsokat, tegyen egy problémát a GitHubon, hogy segíthessünk a probléma megoldásában.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Hiba történt az új kapcsolat hozzáadásaközben: TypeError: Nem olvasható a nem definiált tulajdonság "verziója".

Ha ez a hibaüzenet jelenik meg, amikor egyéni kapcsolatot próbál hozzáadni, a helyi hitelesítő adatok kezelőjében tárolt kapcsolatadatok megsérülhetnek. A probléma kerülő megoldásához próbálja meg a sérült helyi kapcsolatoktörlését, majd adja hozzá újra őket:

1. Indítsa el a Storage Explorer t. A menüben nyissa meg a Súgó**váltása fejlesztői eszközök című témakört.** **Help** > 
2. A megnyitott ablak **Alkalmazás** lapján lépjen a **Helyi tároló** (bal oldalon) > **file://**.
3. Attól függően, hogy milyen típusú kapcsolattal van problémája, keresse meg a kulcsát, majd másolja annak értékét egy szövegszerkesztőbe. Az érték az egyéni kapcsolatnevek tömbje, például a következők:
    * Tárfiókok
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blob-tárolók
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Fájlmegosztások
        * `StorageExplorer_CustomConnections_Files_v1`
    * Üzenetsorok
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Táblák
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Az aktuális kapcsolatnevek mentése után állítsa a `[]`Fejlesztői eszközök értékét a értékre.

Ha meg szeretné őrizni a nem sérült kapcsolatokat, az alábbi lépésekkel megkeresheti a sérült kapcsolatokat. Ha nem bánja az összes meglévő kapcsolat elvesztését, kihagyhatja ezeket a lépéseket, és a platformspecifikus utasításokat követve törölheti a kapcsolatadatait.

1. Egy szövegszerkesztőből adja hozzá újra az egyes kapcsolatok nevét a Fejlesztői eszközökhöz, majd ellenőrizze, hogy a kapcsolat továbbra is működik-e.
2. Ha egy kapcsolat megfelelően működik, az nem sérült, és biztonságosan ott hagyhatja. Ha egy kapcsolat nem működik, távolítsa el annak értékét a Fejlesztői eszközökből, és jegyezze fel, hogy később újra felvehethesse.
3. Ismételje ezt addig, amíg meg nem vizsgálta az összes kapcsolatát.

Miután átnézte az összes kapcsolatot, minden olyan kapcsolatnevét, amely nem lett visszaadva, törölnie kell a sérült adatokat (ha van ilyen), és vissza kell adnia őket a Storage Explorer szokásos lépéseivel:

# <a name="windows"></a>[Windows](#tab/Windows)

1. A **Start** menüben keresse meg a **Hitelesítő adatok kezelőjét,** és nyissa meg.
2. Nyissa meg a **Windows-hitelesítő adatok at**.
3. Az **Általános hitelesítő adatok csoportban** `<connection_type_key>/<corrupted_connection_name>` keresse meg a `StorageExplorer_CustomConnections_Accounts_v1/account1`kulcsot tartalmazó bejegyzéseket (például ).
4. Törölje ezeket a bejegyzéseket, és adja hozzá újra a kapcsolatokat.

# <a name="macos"></a>[Macos](#tab/macOS)

1. Nyissa meg a Reflektorfényt (Command+Szóköz) és keressen **kulcskarika-hozzáférést.**
2. Keresse meg azolyan `<connection_type_key>/<corrupted_connection_name>` bejegyzéseket, `StorageExplorer_CustomConnections_Accounts_v1/account1`amelyekrendelkeznek a kulccsal (például ).
3. Törölje ezeket a bejegyzéseket, és adja hozzá újra a kapcsolatokat.

# <a name="linux"></a>[Linux](#tab/Linux)

A helyi hitelesítő adatok kezelése a Linux-disztribúciótól függően változik. Ha a Linux-disztribúció nem biztosít beépített GRAFIKUS eszközt a helyi hitelesítő adatok kezeléséhez, telepíthet egy külső gyártótól származó eszközt a helyi hitelesítő adatok kezeléséhez. Használhatja például a [Seahorse-t,](https://wiki.gnome.org/Apps/Seahorse/)egy nyílt forráskódú GRAFIKUS eszközt a Linux helyi hitelesítő adatainak kezeléséhez.

1. Nyissa meg a helyi hitelesítő adatokat kezelő eszközt, és keresse meg a mentett hitelesítő adatokat.
2. Keresse meg azolyan `<connection_type_key>/<corrupted_connection_name>` bejegyzéseket, `StorageExplorer_CustomConnections_Accounts_v1/account1`amelyekrendelkeznek a kulccsal (például ).
3. Törölje ezeket a bejegyzéseket, és adja hozzá újra a kapcsolatokat.
---

Ha továbbra is ez a hiba jelenik meg a lépések futtatása után, vagy ha meg szeretné osztani, hogy mi a gyanú sérült a kapcsolatok, [nyisson meg egy problémát](https://github.com/microsoft/AzureStorageExplorer/issues) a GitHub-oldalon.

## <a name="issues-with-sas-url"></a>Problémák a SAS URL-címével

Ha Egy szolgáltatáshoz Egy SAS URL-címén keresztül csatlakozik, és hibát tapasztal:

* Ellenőrizze, hogy az URL biztosítja-e az erőforrások olvasásához vagy listázásához szükséges engedélyeket.
* Ellenőrizze, hogy az URL-cím nem járt-e le.
* Ha a SAS URL-címe hozzáférési házirenden alapul, ellenőrizze, hogy a hozzáférési házirendet nem vonták-e vissza.

Ha véletlenül érvénytelen SAS-URL-cím használatával csatolta, és most nem tud leválasztani, kövesse az alábbi lépéseket:

1. A Storage Explorer futtatásakor nyomja le az F12 billentyűt a Fejlesztőeszközök ablak megnyitásához.
2. Az **Alkalmazás** lapon válassza a **Helyi tároló** > **file://** a bal oldali fán.
3. Keresse meg a problémás SAS URI szolgáltatástípusához társított kulcsot. Ha például a hibás SAS URI egy blobtárolóhoz `StorageExplorer_AddStorageServiceSAS_v1_blob`van, keresse meg a nevű kulcsot.
4. A kulcs értékének JSON-tömbnek kell lennie. Keresse meg a hibás URI-hoz társított objektumot, majd törölje azt.
5. A Tárterület-kezelő újratöltéséhez nyomja le a Ctrl+R billentyűkombinációt.

## <a name="linux-dependencies"></a>Linux-függőségek

A Storage Explorer 1.10.0-s és újabb verziói a Snap Store áruházból pillanatalatt érhetők el. A Tárolókezelő dokkolása automatikusan telepíti az összes függőségét, és frissül, ha a snap új verziója elérhetővé válik. A Storage Explorer snap telepítése az ajánlott telepítési módszer.

A Tárolókezelő jelszókezelő használatát igényli, amelyet előfordulhat, hogy manuálisan kell csatlakoztatnia, mielőtt a Tárolókezelő megfelelően működne. A Storage Explorer t a rendszer jelszókezelőjéhez a következő parancs futtatásával csatlakoztathatja:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Az alkalmazást .tar.gz fájlként is letöltheti, de a függőségeket manuálisan kell telepítenie.

> [!IMPORTANT]
> A .tar.gz letöltésben megadott Storage Explorer csak az Ubuntu disztribúciók esetében támogatott. Más disztribúciók nem lettek ellenőrizve, és alternatív vagy további csomagokra lehet szükség.

Ezek a csomagok a Leggyakoribb követelmények Storage Explorer Linuxon:

* [.NET Core 2.2 futásidejű](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` vagy `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> A Storage Explorer 1.7.0-s és korábbi verziója esetén a .NET Core 2.0 szükséges. Ha a .NET Core újabb verziója van telepítve, akkor a Storage Explorer programot kell [kijavítania.](#patching-storage-explorer-for-newer-versions-of-net-core) Ha a Storage Explorer 1.8.0-s vagy újabb verziót használja, legfeljebb .NET Core 2.2-t kell használnia. A 2.2-es verziók jelenleg nem működnek.

# <a name="ubuntu-1904"></a>[Ubuntu 19.04](#tab/1904)

1. Töltse le a Tárházkezelőt.
2. Telepítse a [.NET Core Runtime -ot](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Futtassa az alábbi parancsot:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Töltse le a Tárházkezelőt.
2. Telepítse a [.NET Core Runtime -ot](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Futtassa az alábbi parancsot:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Töltse le a Tárházkezelőt.
2. Telepítse a [.NET Core Runtime -ot](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Futtassa az alábbi parancsot:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[Ubuntu 14.04](#tab/1404)

1. Töltse le a Tárházkezelőt.
2. Telepítse a [.NET Core Runtime -ot](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Futtassa az alábbi parancsot:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>A Storage Explorer javítása a .NET Core újabb verzióihoz

A Storage Explorer 1.7.0-s vagy korábbi verziójában előfordulhat, hogy a Storage Explorer által használt .NET Core verziót kell javítania:

1. Töltse le a StreamJsonRpc 1.5.43-as verzióját [a NuGet-től.](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) Keresse meg a "Csomag letöltése" linket az oldal jobb oldalán.
2. A csomag letöltése után módosítsa `.nupkg` a `.zip`fájlkiterjesztését a-ra.
3. Csomagolja ki a csomagot.
4. Nyissa `streamjsonrpc.1.5.43/lib/netstandard1.1/` meg a mappát.
5. Másolás `StreamJsonRpc.dll` a Storage Explorer mappa következő helyeire:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Az Azure Portal "Open In Explorer" nem működik

Ha az Azure Portalon az **Open In Explorer** gomb nem működik, ellenőrizze, hogy kompatibilis böngészőt használ-e. A következő böngészők kompatibilitását tesztelték:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>További lépések

Ha a megoldások egyike sem működik, [nyisson meg egy problémát a GitHubon.](https://github.com/Microsoft/AzureStorageExplorer/issues) Ezt úgy is megteheti, hogy a bal alsó sarokban a **GitHub jelentésének kérdését** választja.

![Visszajelzés](./media/storage-explorer-troubleshooting/feedback-button.PNG)
