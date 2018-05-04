---
title: Az Azure Tártallózó hibaelhárítási útmutatója |} Microsoft Docs
description: A két funkció az Azure-hibakeresés áttekintése
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: f58fb5090aba3c5052d1bbdec76225d0ae50e8f2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Az Azure Tártallózó hibaelhárítási útmutató

A Microsoft Azure Tártallózó egy önálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, a macOS és a Linux. Az alkalmazás Azure nemzeti felhők és Azure verem üzemeltetett Storage-fiókok is elérheti.

Ez az útmutató a megoldások a Tártallózó tapasztalt gyakori problémákat foglalja össze.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Hiba: Önaláírt tanúsítvány láncát (és hasonló hibák)

Hitelesítési hibák okozzák a két alábbi esetekben:

1. Az alkalmazás "transzparens proxyra", vagyis a kiszolgálóhoz (például a vállalati kiszolgálónak) elfogja a HTTPS-forgalmat, visszafejtése, és azt egy önaláírt tanúsítványt használ majd titkosítása keresztül csatlakozik.
2. Van egy önaláírt SSL-tanúsítványt a HTTPS fogadott üzenetek hogy alkalmazást futtat. Alkalmazás, amely tanúsítványokat például víruskereső és a hálózati forgalom hálózatfelügyeleti szoftvert tartalmaz.

Amikor a Tártallózó látja egy aláírt önaláírt vagy nem megbízható tanúsítvány, azt már nem tudja, hogy módosult-e a fogadott üzenet HTTPS. Ha az önaláírt tanúsítvány egy példányát, utasíthatja Tártallózó megbízható a következő lépések végrehajtásával:

1. Szerezzen be egy Base-64 kódolású X.509 (.cer) a kiválasztott tanúsítvány másolatát
2. Kattintson a **szerkesztése** > **SSL-tanúsítványok** > **importálási tanúsítványok**, majd a fájlkiválasztóval található, válassza ki, majd nyissa meg a .cer fájlt, és

A probléma is több tanúsítvány (gyökér és köztes) eredményét. A hiba megoldásához mindkét tanúsítványnak szerepelnie kell.

Ha biztos abban, ahol a tanúsítvány érkezik, próbálja meg ezeket a lépéseket, és keresse meg:

1. Telepítse az Open SSL-t.

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (könnyű verzióinak elegendőnek kell lennie)
    * Mac- és Linux: kell figyelembe venni az operációs rendszer
2. Futtassa az Open SSL-t.

    * Windows: a telepítési könyvtár megnyitásához kattintson **/bin/**, majd kattintson duplán **openssl.exe**.
    * Mac- és Linux: futtatása **openssl** terminálról.
3. Hajtsa végre az `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha biztos benne, amelyeket önaláírt, keressen bárhol a tulajdonos `("s:")` és kiállító `("i:")` megegyezik.
5. Miután megtalálta az összes önaláírt tanúsítványokat, mindegyikhez, másolja be minden-kra **---BEGIN CERTIFICATE---** való **---vége tanúsítvány---** egy új .cer kiterjesztésű fájlba.
6. Nyissa meg a Tártallózót, kattintson a **szerkesztése** > **SSL-tanúsítványok** > **importálási tanúsítványok**, és majd a fájlkiválasztóval található, válassza ki, majd nyissa meg a létrehozott .cer kiterjesztésű fájlokat.

Ha nem találja az előző lépéseket követve bármely önaláírt tanúsítványokat, kapcsolatfelvétel a visszajelzés eszközzel további segítséget itt találhat. Azt is megteheti, ha szeretné, indítsa el a Tártallózót parancsot a parancssorból a `--ignore-certificate-errors` jelzőt. Ez a jelző indításakor Tártallózó figyelmen kívül hagyja hitelesítési hibák.

## <a name="sign-in-issues"></a>Bejelentkezési problémák

Ha nem sikerül bejelentkezni, próbálkozzon az alábbi hibaelhárítási módszerek:

* Indítsa újra a Tártallózó alkalmazással
* Ha a hitelesítési ablak üres, várja meg a párbeszédpanel bezárása előtt legalább egy percig.
* Győződjön meg arról, hogy a proxy- és megfelelőek-e a gép és a Tártallózó tanúsítvány
* Ha a Windows és a ugyanazon a számítógépen, valamint bejelentkezési Visual Studio 2017 hozzáférése, próbáljon meg bejelentkezni a Visual Studio 2017

Ha a fenti módszerek egyikével sem működik [nyissa meg a problémát a Githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="unable-to-retrieve-subscriptions"></a>Az előfizetéseket nem sikerül lekérni

Ha nem sikerült beolvasni az előfizetések sikeres bejelentkezést, próbáljon a következő hibaelhárítási módszerek:

* Győződjön meg arról, hogy a fiók rendelkezik-e a várt előfizetéseket a hozzáférést. Ellenőrizheti, hogy van-e hozzáférése az Azure környezetbe használni kívánt portált aláírásával.
* Győződjön meg arról, hogy a megfelelő Azure használatával regisztrált környezet (Azure, Azure Kína, Azure Németország, Azure Amerikai Egyesült államokbeli kormányzati vagy egyéni környezet).
* Ha a rendszer proxy mögött, győződjön meg arról, hogy a Tártallózó proxy megfelelően van konfigurálva.
* Próbálja meg eltávolítani, és olvasása a következő fiók.
* Tekintse meg a fejlesztői eszközök konzol (Súgó > Toggle fejlesztői eszközök) Tártallózó előfizetések betöltése közben. Hiba üzenetekben (piros), vagy bármely tartalmazó üzenet "nem tölthető be a bérlői előfizetések." Ha minden vonatkozó üzenet [nyissa meg a problémát a Githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Csatolt fiók vagy a storage erőforrás nem távolítható el.

Ha nem tudja eltávolítani a csatlakoztatott partner vagy tárolási erőforrás a felhasználói felületen, manuálisan törölheti az összes csatolt erőforrások törölni kell a következő mappák:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  A fenti mappák törlése előtt zárja be Tártallózóval.

> [!NOTE]
>  Ha valaha is importálta-e SSL-tanúsítványokat, majd biztonsági mentését a tartalmát a `certs` könyvtár. Később a biztonsági mentés használatával importálja újra az SSL-tanúsítványokat.

## <a name="proxy-issues"></a>Proxy problémák

Először is győződjön meg arról, hogy minden helyesen-e a következő adatokat a megadott:

* A proxykiszolgáló URL-CÍMÉT és portszámát * felhasználónév és a jelszót, ha az szükséges a proxy

### <a name="common-solutions"></a>Közös megoldások

Ha továbbra is problémákat tapasztal, próbálja meg a következő hibaelhárítási módszerek:

* Ha a proxy használata nélkül is csatlakozni az internethez, ellenőrizze, hogy működik-e a Tártallózó nélkül proxy-beállítások engedélyezve vannak. Ha ez a helyzet, előfordulhat, hogy a proxybeállítások problémát kell. A proxykiszolgáló rendszergazdájához, és a problémák azonosításához dolgozni.
* Győződjön meg arról, hogy más alkalmazások, amelyek a proxykiszolgálót a várt módon működik-e.
* Győződjön meg arról, hogy tud-e csatlakozni az környezetet használni kívánt Azure-portálon
* Győződjön meg arról, hogy fogadhat válaszok a végpontok. Adja meg a végpont URL-címek egyikét a böngészőbe. Ha csatlakoztat, egy InvalidQueryParameterValue vagy hasonló XML-válasz kell kapnia.
* Ha a proxykiszolgáló valaki más is használja a Tártallózó, győződjön meg arról, hogy csatlakozhassanak. Ha a csatlakozás, előfordulhat, hogy kapcsolatba a proxy server rendszergazdával.

### <a name="tools-for-diagnosing-issues"></a>A problémák diagnosztizálásával eszközök

Ha hálózati eszközök, például a Fiddler a Windows, esetleg a problémák diagnosztizálásához az alábbiak szerint:

* Ha a proxyn keresztül történő működéséhez, előfordulhat, hogy a hálózati eszköz csatlakozni a proxyn keresztül történő konfigurálásához.
* Ellenőrizze a hálózati eszköz által használt port számát.
* Adja meg a helyi állomás URL-cím és a hálózati eszköz portszám Tártallózó proxybeállításai. A megfelelően végzett, a hálózati eszköz elindítja a hálózati kérelmek, felügyeleti és Szolgáltatásvégpontok Tártallózó által végzett naplózás. Adja meg például https://cawablobgrs.blob.core.windows.net/ kap a blob-végpontot egy böngészőben, és a válasz a következőhöz, ami alapján, az erőforrás létezik-e, bár nem férhet hozzá.

![kódminta](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Lépjen kapcsolatba a proxy-kiszolgálói rendszergazda

Ha a proxybeállításai megfelelőek, előfordulhat, hogy a proxy server rendszergazdától, és

* Győződjön meg arról, hogy a proxy blokkolja Azure felügyeleti vagy erőforrás-végpontok irányuló forgalmat.
* Ellenőrizze a proxy-kiszolgáló által használt hitelesítési protokoll. A Tártallózó jelenleg nem támogatja az NTLM-proxyk.

## <a name="unable-to-retrieve-children-error-message"></a>"Nem sikerült beolvasni a gyermekek" hibaüzenet jelenik meg

Ha proxyn keresztül csatlakoznak az Azure-ba, győződjön meg arról, hogy a proxybeállítások helyességéről. Ha az előfizetés vagy a fiók tulajdonosának a hozzáférési volt engedélyezni lehessen egy erőforrást, győződjön meg arról, olvasási, vagy erőforrás engedélyeinek listázása

### <a name="issues-with-sas-url"></a>SAS URL-cím problémái
Ha a szolgáltatás egy SAS URL-cím segítségével, és ezt a hibát tapasztaló csatlakozik:

* Győződjön meg arról, hogy az URL-cím biztosít-e olvasási és erőforrások sorolja fel a szükséges engedélyekkel.
* Győződjön meg arról, hogy az URL-cím nem járt le.
* Ha a hozzáférési házirendek az SAS URL-cím alapú, győződjön meg arról, hogy a házirend nincs visszavonva.

Ha véletlenül egy SAS URL-cím érvénytelen keresztül kapcsolódik, és nem lehet leválasztani, kövesse az alábbi lépéseket:
1.  A Tártallózó futtatásakor nyomja le az F12 billentyűt a fejlesztői eszközök ablak megnyitásához.
2.  Kattintson az alkalmazás fülre, majd kattintson a helyi tároló > file:// a bal oldali fában.
3.  A szolgáltatás típusa a problematikus SAS URI-társított kulcs található. Ha a hibás SAS URI blob-tároló, keresse meg például a kulcs nevű `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  A kulcs értékének kell egy JSON-tömb. A hibás URI-azonosítóhoz tartozó objektum található, és távolítsa el.
5.  Töltse be újra a Tártallózó Ctrl + R billentyűkombinációt.

## <a name="linux-dependencies"></a>Linux-függőségek

A Linux disztribúciókkal eltérő Ubuntu 16.04 szükség lehet manuálisan kell telepítenie néhány függősége. Általában a következő csomagok szükségesek:
* [A .NET core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* Naprakész ÖET

Attól függően, hogy a distro lehet többi csomagot, telepítenie kell. A Tártallózó [kibocsátási megjegyzések](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) néhány disztribúciókkal az adott lépést tartalmaznak.

## <a name="next-steps"></a>További lépések

Ha a megoldások egyike sem működik, majd [nyissa meg a problémát a Githubon](https://github.com/Microsoft/AzureStorageExplorer/issues). Gyorsan is kaphat a GitHub a "GitHub problémát jelentés" gombra kattintva a bal alsó sarkában.

![Visszajelzés](./media/storage-explorer-troubleshooting/feedback-button.PNG)
