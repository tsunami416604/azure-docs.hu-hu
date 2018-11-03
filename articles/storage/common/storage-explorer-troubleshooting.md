---
title: Azure Storage Explorer – hibaelhárítási útmutató |} A Microsoft Docs
description: A két Hibakeresés az Azure szolgáltatás áttekintése
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.component: common
ms.openlocfilehash: fa73062cc2fdfa3704088f37b2e4856e134a6dfe
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979029"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer – hibaelhárítási útmutató

A Microsoft Azure Storage Explorer egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken. Az alkalmazás üzemeltetett Azure, az országos felhők és az Azure Stack tárfiókok csatlakozhat.

Ez az útmutató összefoglalja a Storage Explorer leggyakoribb problémáinak megoldásait.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Chyba: Önaláírt tanúsítványt a tanúsítványlánc (és ehhez hasonló hibaüzenetek)

Tanúsítvánnyal kapcsolatos hiba okozza a két alábbi helyzetek bármelyike:

1. Az alkalmazás egy "transzparens proxy", ami azt jelenti, hogy a kiszolgáló (például a vállalati kiszolgálónak) elfogja a HTTPS-forgalmat, visszafejti azt, és majd újra titkosítja egy önaláírt tanúsítvány használatával keresztül csatlakozik.
2. Olyan alkalmazás, amely egy önaláírt SSL-tanúsítvány van injektálásra a fogadott HTTPS üzenetek futnak. Tanúsítványok beszúrása alkalmazások magában foglalja a víruskereső és a hálózati forgalom ellenőrzési szoftvereket.

Amikor a Storage Explorer látja a saját aláírt vagy nem megbízható tanúsítványt, akkor már nem tudja, a fogadott HTTPS-üzenetet módosították-e. Ha az önaláírt tanúsítvány egy példányát, utasíthatja Storage Explorer megbízzon benne a következő lépések végrehajtásával:

1. Szerezzen be egy Base-64 kódolású X.509 (.cer) a tanúsítvány másolatát
2. Kattintson a **szerkesztése** > **SSL-tanúsítványok** > **tanúsítványok importálása**, majd a Fájlkereső segítségével keresse meg, válassza ki, nyissa meg a .cer fájlt

A probléma is előfordulhat, hogy több tanúsítványt (gyökér és köztes) eredménye. A hiba, hogy mindkét tanúsítvány hozzá kell adni.

Ha bizonytalan, a tanúsítvány forrását, megpróbálhatja, megtalálhatja ezeket a lépéseket:

1. Telepítse az Open SSL-t.

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (bármelyik egyszerűsített verzió elegendőnek kell lennie)
    * Mac és Linux: elvileg eleve mellékelve van az operációs rendszer
2. Futtassa az Open SSL-t.

    * Windows: Nyissa meg a telepítési könyvtárát, kattintson a **/bin/**, majd kattintson duplán **openssl.exe**.
    * Mac és Linux: futtassa **openssl** parancsot egy terminálról.
3. Hajtsa végre az `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha biztos benne, amelyeket a önaláírt, keressen bárhol a tulajdonos `("s:")` és kiállító `("i:")` azonos.
5. Ha talált önaláírt tanúsítványokat, másolja ki a is beleértve **---BEGIN CERTIFICATE---** való **---END CERTIFICATE---** egy új .cer fájlba.
6. Nyissa meg a Storage Explorerben, kattintson a **szerkesztése** > **SSL-tanúsítványok** > **tanúsítványok importálása**, majd a Fájlkereső segítségével keresse meg, válassza ki, és Nyissa meg a létrehozott .cer fájlokat.

Ha nem talál önaláírt tanúsítványokat használ a fenti lépéseket, a visszajelzési eszközzel, további segítségért lépjen kapcsolatba velünk. Másik lehetőségként választhatja a parancssorból indítsa el a Storage Explorer a `--ignore-certificate-errors` jelzőt. Ez a jelző indításakor Storage Explorer tanúsítvánnyal kapcsolatos hiba figyelmen kívül.

## <a name="sign-in-issues"></a>Bejelentkezési problémák

### <a name="reauthentication-loop-or-upn-change"></a>Hurok újrahitelesítést vagy egyszerű felhasználónév módosítása
Ha a hurok újrahitelesítést, vagy módosította az egyik a fiók egyszerű Felhasználóneve, megpróbálkozhat a következőkkel:
1. Távolítsa el az összes fiókot, és zárja be a Storage Explorerben
2. Törölje a. IdentityService mappájában található a gépen. A Windows, a mappa a következő helyen található `C:\users\<username>\AppData\Local`. A mappa gyökerénél legyen a felhasználói címtár annak Macre és Linuxra készült.
3. Ha Mac vagy Linux rendszeren, is szüksége lesz a Microsoft.Developer.IdentityService tétel törlését az operációs rendszer keystore. A kulcstár Mac, a "Kulcslánc Gnome" alkalmazás, amely. A Linux rendszerre az alkalmazás általában neve "Kulcstár", de lehet, hogy a név attól függően változik, a terjesztési.

## <a name="mac-keychain-errors"></a>Mac kulcslánc-hibák
A macOS kulcslánc néha kérheti le a Storage Explorer hitelesítési tár problémákat okozó állapotba. Lekérése a kulcsláncban a állapot próbálja ki az alábbi lépéseket:
1. Zárja be a Storage Explorerben.
2. Nyissa meg a kulcslánc (**cmd + szóköz**, írja be a kulcslánc, találati adja meg).
3. Válassza ki a "bejelentkezés" kulcslánc.
4. Kattintson a lakat ikonra, zárolja a kulcsláncban (a lakat a zárolt helyzetben, amikor végzett, eltarthat néhány másodpercig függően rendelkezik alkalmazások nyissa meg a rendszer animálása).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Indítsa el a Storage Explorerben.
6. Egy előugró ablak akkor is meg kell jelennie üzenettel, ha valami, például a "Service hub szeretne hozzáférni a keychain". Amikor azt, Mac-rendszergazdai fiók jelszavát adja meg, és kattintson a **mindig** (vagy **engedélyezése** Ha **mindig** nem érhető el).
7. Próbáljon meg bejelentkezni.

### <a name="general-sign-in-troubleshooting-steps"></a>Általános bejelentkezési hibaelhárítási lépéseket
* Ha MacOS-gépeken, és a bejelentkezési ablakban soha nem a "Hitelesítés... Várakozás" párbeszédpanelen keresztül jelenik meg, majd próbálja meg [ezeket a lépéseket](#mac-keychain-errors)
* Indítsa újra a Tártallózót
* Ha a hitelesítési ablak üres, várjon legalább egy percet, mielőtt bezárná a hitelesítési párbeszédpanelt.
* Győződjön meg arról, hogy a proxy- és a tanúsítvány beállításait konfigurálja megfelelően a gép és a Storage Explorer.
* Ha a Windows és a Visual Studio 2017 hozzáférést a gép és a bejelentkezési rendelkezik, próbáljon meg, a Visual Studio 2017 bejelentkezni. A sikeres bejelentkezést követően a Visual Studio 2017 nyissa meg a Storage Explorert, és látja: saját fiók a fiók panel képesnek kell lennie. 

Ha ezen metódusok közül egyik sem működik [nyisson egy problémát a Githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Hiányzó előfizetések és a bérlők megszakadt

Ha nem tudja lekérni az előfizetéseit, miután sikeresen bejelentkezett, próbálja meg a következő hibaelhárítási módszerek:

* Győződjön meg arról, hogy a fiók rendelkezik-e hozzáférése az előfizetésekhez várt. Ellenőrizheti, hogy rendelkezik-e hozzáféréssel a használni kívánt Azure-környezetre vonatkozó portálra való bejelentkezés révén.
* Győződjön meg arról, hogy bejelentkezett, az a megfelelő Azure-környezet (Azure, Azure China, az Azure Germany, Azure US Government vagy egyéni környezet).
* Ha proxy mögött található, győződjön meg arról, hogy a Storage Explorer-proxy megfelelően van konfigurálva.
* Próbálja meg eltávolítani és újra hozzáadja a fiókot.
* Ha a "További információ" hivatkozást, keresse meg, és tekintse meg, milyen hibaüzenetek a bérlők számára, amelyek nem jelentették. Ha nem biztos, hogy mi a hibaüzeneteket, tekintse meg, akkor nyugodtan [nyisson egy problémát a Githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Csatolt fiók vagy a storage erőforrás nem távolítható el.

Ha Ön nem távolítható el egy csatolt fiók vagy a tárolási erőforrás a felhasználói felületen, manuálisan törölheti az összes kapcsolódó erőforrás a következő mappák törlésével:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  A fenti mappák törlése előtt zárja be a Storage Explorerben.

> [!NOTE]
>  Ha minden eddiginél importálta-e SSL-tanúsítványok, akkor biztonsági mentése a tartalmát a `certs` könyvtár. Később a biztonsági mentés használatával importálja újra az SSL-tanúsítványokat.

## <a name="proxy-issues"></a>Proxy kapcsolatos problémák

Először is győződjön meg arról, hogy minden helyesen-e a megadott a következő információkat:

* A proxykiszolgáló URL-cím és port száma
* Felhasználónév és jelszó, ha a proxy által igényelt

### <a name="common-solutions"></a>Általános megoldások

Ha továbbra is problémákat tapasztal, próbálja meg a következő hibaelhárítási módszerek:

* Ha a proxy használata nélkül is csatlakozni az internethez, ellenőrizze a Storage Explorer nélkül engedélyezve proxybeállítások működését. Ez a helyzet, ha a proxybeállításai probléma lehet. A problémák azonosítását a proxy rendszergazdával együttműködve.
* Győződjön meg arról, hogy a proxykiszolgáló használatát más alkalmazások a várt módon működik-e.
* Győződjön meg arról, hogy képes-e csatlakozni a portálon használni kívánt Azure-környezetre vonatkozó
* Győződjön meg arról, hogy kaphat szolgáltatásvégpontokra érkező válaszok. Adja meg a végpont URL-címek egyikét a böngészőbe. Csatlakoztathatja, ha egy InvalidQueryParameterValue vagy hasonló XML-választ kell kapnia.
* Ha valaki más is használja a Tártallózó fel a proxykiszolgálón, győződjön meg arról, hogy csatlakozhassanak. Csatlakozás, ha előfordulhat, forduljon a proxy server rendszergazdához.

### <a name="tools-for-diagnosing-issues"></a>Eszközök a problémák diagnosztizálása

Ha hálózati eszközök, például a Fiddlert a Windows, lehetnek a problémák diagnosztizálása a következő:

* Ha a proxyn keresztül működik, előfordulhat, a hálózati eszközzel csatlakozzon a proxyn keresztül történő konfigurálásához.
* Ellenőrizze a hálózati eszköz által használt port számát.
* Adja meg a helyi állomás URL-CÍMÉT és a hálózati eszköz portszám a Storage Explorerben proxykiszolgáló-beállításként. A megfelelően végzett a hálózati eszköz elindul, hálózati kérést hoz létre a felügyeleti és Szolgáltatásvégpontok Storage Explorer-naplózás. Adja meg például https://cawablobgrs.blob.core.windows.net/ a blob-végpont a böngészőben, és meg fogja kapni a válasz a következőhöz, ami alapján a az erőforrás létezik, de nem tudja elérni.

![Kódminta](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Lépjen kapcsolatba a proxy server-rendszergazda

Ha a proxybeállításai megfelelőek, lehet a proxy server rendszergazdához, és

* Győződjön meg arról, hogy a proxykiszolgáló nem blokkolja-e az Azure felügyeleti vagy az erőforrás-végpontokra irányuló forgalmat.
* Ellenőrizze a proxy-kiszolgáló által használt hitelesítési protokoll. Storage Explorer jelenleg nem támogatja az NTLM-proxyk.

## <a name="unable-to-retrieve-children-error-message"></a>"A gyermekek lekérése nem sikerült" hibaüzenet jelenik meg

Ha proxyn keresztül csatlakoznak az Azure-ba, győződjön meg arról, hogy helyesek-e a WebProxy beállításait. Ha a fiók vagy előfizetés tulajdonosától is kapott hozzáférést egy erőforráshoz, győződjön meg arról, hogy rendelkezik-e olvasási vagy listában meghatározott engedélyek.

## <a name="connection-string-does-not-have-complete-configuration-settings"></a>Kapcsolati karakterlánc nem rendelkezik teljes konfigurációs beállításai

Ha ezt a hibaüzenetet kapja, akkor lehet, hogy nem kell a szükséges engedélyekkel a tárfiók kulcsainak lekéréséhez. Győződjön meg arról, ha ez a helyzet, lépjen a portálra, és keresse meg a Storage-fiókjában. A tárfiók a csomóponton kattintson jobb gombbal, majd kattintson a "Nyissa meg a portál" gyorsan teheti ezt. Ezt követően nyissa meg a "Hozzáférési kulcsok" panel. Ha nem rendelkezik engedéllyel a kulcsok megtekintéséhez majd megjelenik egy oldal a "Nem rendelkezik hozzáféréssel" üzenettel. Megkerülő megoldás a probléma, lehetősége van a fiókkulcs szerzi valaki más, majd csatolása nevére és kulcsára, vagy kérjen egy SAS a tárfiókra, és csatlakoztassa a Storage-fiók használatával.

Ha a fiók kulcsok megtekintéséhez, majd nyújtson egy problémát a Githubon, így a segítségére a probléma megoldásához.

## <a name="issues-with-sas-url"></a>Problémák az SAS URL-címe
Ha egy szolgáltatás SAS URL-címet használ, és ezt a hibát tapasztaló csatlakozik:

* Győződjön meg arról, hogy az URL-címet biztosít-e olvasási és erőforrások listázása a szükséges engedélyekkel.
* Győződjön meg arról, hogy az URL-cím nem járt le.
* Ha az SAS URL-cím alapján egy hozzáférési szabályzatot, győződjön meg arról, hogy a hozzáférési szabályzat nem vissza lett vonva.

Ha véletlenül egy érvénytelen SAS URL-címet használó kapcsolt, és nem lehet leválasztani, kövesse az alábbi lépéseket:
1.  Storage Explorer futtatásakor nyomja le az F12 billentyűt a fejlesztői eszközök ablak megnyitásához.
2.  Kattintson az alkalmazás lapon, majd kattintson a helyi tároló > file:// a bal oldali fában.
3.  Keresse meg a szolgáltatás típusa az problémás SAS URI társított kulcsot. Ha a hibás SAS URI blob tárolóhoz, keresse meg például a nevű kulcs `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  A kulcsnak az értéke egy JSON-tömböt kell lennie. A hibás URI társított objektum található, és távolítsa el azt.
5.  Töltse be újra a Storage Explorer a Ctrl + R billentyűkombinációt.

## <a name="linux-dependencies"></a>Linux-függőségek

A Linux-disztribúciók Ubuntu 16.04 eltérő szükség lehet néhány függősége manuális telepítéséhez. Általában a következő csomagok szükségesek:
* [A .NET core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* Naprakész GCC

A disztribúció függően más csomagokat telepítenie kell is lehet. A Storage Explorer [kibocsátási megjegyzések](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) néhány disztribúciókhoz az adott lépést tartalmaznak.

## <a name="next-steps"></a>További lépések

Ha a megoldások egyike sem működik, majd [nyisson egy problémát a Githubon](https://github.com/Microsoft/AzureStorageExplorer/issues). Is gyorsan kaphat a Githubra bal alsó sarokban lévő "Jelentés problémát a Githubban" gomb segítségével.

![Visszajelzés](./media/storage-explorer-troubleshooting/feedback-button.PNG)
