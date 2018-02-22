---
title: "Az Azure Tártallózó hibaelhárítási útmutatója |} Microsoft Docs"
description: "A két funkció az Azure-hibakeresés áttekintése"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: 2f62de428d1915b1e070350a2837f24c3486f8c7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Az Azure Tártallózó hibaelhárítási útmutató

A Microsoft Azure Tártallózó (előzetes verzió) egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, a macOS és a Linux. Az alkalmazás Azure nemzeti felhők és Azure verem üzemeltetett Storage-fiókok is elérheti.

Ez az útmutató a megoldások a Tártallózó tapasztalt gyakori problémákat foglalja össze.

## <a name="sign-in-issues"></a>Jelentkezzen be a problémák

Csak az Azure Active Directory (AAD) fiókok támogatottak. Ha az AD FS fiókot használjon, várható, hogy a Tártallózó bejelentkezés csatlakoztatás nem működik. A folytatás előtt indítsa újra az alkalmazást, és hogy kell-e javítani a problémák.

### <a name="error-self-signed-certificate-in-certificate-chain"></a>Hiba: A tanúsítványláncában szereplő önaláírt tanúsítvány

Több oka miért merülhetnek fel ezt a hibát, és a leggyakrabban használt két lehetnek az okai a következők:

1. Az alkalmazás "transzparens proxyra", vagyis a kiszolgálóhoz (például a vállalati kiszolgálónak) elfogja a HTTPS-forgalmat, visszafejtése, és azt egy önaláírt tanúsítványt használ majd titkosítása keresztül csatlakozik.

2. Egy alkalmazás, például víruskereső szoftver, amely egy önaláírt SSL-tanúsítvány van hogy megkapja a HTTPS-üzenetek futtatja.

Amikor Tártallózó egyik hibát észlel, azt már nem tudja, hogy a fogadott üzenet HTTPS van-e illetéktelenül. Ha az önaláírt tanúsítvány egy példányát, hogy a Tártallózó megbízható. Ha biztos a tanúsítvány ki van beszúrva, kövesse az alábbi lépéseket, és keresse meg:

1. Nyissa meg az SSL telepítése

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (könnyű verzióinak elegendőnek kell lennie)

    - Mac- és Linux: kell figyelembe venni az operációs rendszer

2. Nyissa meg az SSL futtatása

    - Windows: a telepítési könyvtár megnyitásához kattintson **/bin/**, majd kattintson duplán **openssl.exe**.
    - Mac- és Linux: futtatása **openssl** terminálról.

3. Végrehajtás s_client - showcerts-csatlakozás microsoft.com:443

4. Keresse meg az önaláírt tanúsítványokat. Ha biztos benne, amelyek van látva saját aláírással, keressen tetszőleges helyre a tárgy ("%s") és a kiállító ("i:") azonos.

5. Miután megtalálta az összes önaláírt tanúsítványokat, mindegyikhez, másolja be minden-kra **---BEGIN CERTIFICATE---** való **---vége tanúsítvány---** egy új .cer kiterjesztésű fájlba.

6. Nyissa meg a Tártallózót, kattintson a **szerkesztése** > **SSL-tanúsítványok** > **importálási tanúsítványok**, és majd a fájlkiválasztóval található, válassza ki, majd nyissa meg a létrehozott .cer kiterjesztésű fájlokat.

Ha nem találja az előző lépéseket követve bármely önaláírt tanúsítványokat, kapcsolatfelvétel a visszajelzés eszközzel további segítséget itt találhat.

### <a name="unable-to-retrieve-subscriptions"></a>Nem sikerült beolvasni az előfizetések

Ha nem tudja beolvasni az előfizetések sikeres bejelentkezés után, kövesse az alábbi lépéseket a probléma elhárítása érdekében:

- Győződjön meg arról, hogy a fiók hozzáfér az előfizetések az Azure-portál bejelentkezni.

- Győződjön meg arról, hogy be van jelentkezve a megfelelő környezet (Azure, Azure Kína, Azure Németország, Azure Amerikai Egyesült államokbeli kormányzati vagy egyéni környezet vagy az Azure-verem) használatával.

- Ha a rendszer proxy mögött, győződjön meg arról, hogy a Tártallózó proxy megfelelően van konfigurálva.

- Próbálja meg eltávolítani, és olvasása a következő fiók.

- Próbálja meg a következő fájlok eltávolítása a gyökérkönyvtár (Ez azt jelenti, hogy C:\Users\ContosoUser), majd olvasása a következő fiók:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Tekintse meg a fejlesztői eszközök konzol (az F12 billentyű megnyomásával) hibaüzeneteket a bejelentkezés során:

![fejlesztői eszközök](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>Nem sikerült a hitelesítés oldal jelenik meg

Ha nem sikerül, a hitelesítés lap, kövesse az alábbi lépéseket a probléma elhárítása érdekében:

- Attól függően, hogy a kapcsolat sebességétől a bejelentkezési lap betöltése, és a párbeszédpanel bezárása előtt legalább egy percig várjon egy ideig is eltarthat.

- Ha a rendszer proxy mögött, győződjön meg arról, hogy a Tártallózó proxy megfelelően van konfigurálva.

- A fejlesztői konzolján megtekintheti az F12 billentyű lenyomása mellett. Tekintse meg a fejlesztői konzolján válaszát, és ellenőrizze, hogy található bármely clue ennek okát a hitelesítés nem működik.

### <a name="cannot-remove-account"></a>Sikertelen volt a fiók eltávolítása

Ha nem tudja eltávolítani egy fiókot, vagy az újrahitelesítés elemre hivatkozás nem befolyásolja, kövesse az alábbi lépéseket a probléma elhárítása érdekében:

- Próbálja meg a következő fájlok eltávolítása a gyökérkönyvtár, és ezután olvasása a következő fiók:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Ha el szeretné távolítani a SAS csatlakoztatott tároló-erőforrások, törölje a következő fájlokat:

    - A Windows %AppData%/StorageExplorer mappa

    - /Felhasználók/ < sajat_nev >/Library/Applicaiton támogatási/StorageExplorer Mac rendszerre

    - ~/.config/StorageExplorer for Linux

> [!NOTE]
>  Ha ezeket a fájlokat töröl, szüksége lesz jelentkezzen be újra a a fiókjait.

## <a name="proxy-issues"></a>Proxy problémák

Először is győződjön meg arról, hogy minden helyesen-e a következő adatokat a megadott:

- A proxykiszolgáló URL-cím és port száma

- Felhasználónevet és jelszót, ha az szükséges a proxy

### <a name="common-solutions"></a>Közös megoldások

Ha továbbra is problémákat tapasztal, kövesse az alábbi lépéseket a hibakereséshez:

- Ha a proxy használata nélkül is csatlakozni az internethez, ellenőrizze, hogy működik-e a Tártallózó nélkül proxy-beállítások engedélyezve vannak. Ha ez a helyzet, előfordulhat, hogy a proxybeállítások problémát kell. A proxykiszolgáló rendszergazdájához, és a problémák azonosításához dolgozni.

- Győződjön meg arról, hogy más alkalmazások, amelyek a proxykiszolgálót a várt módon működik-e.

- Ellenőrizze, hogy tud-e csatlakozni a webböngésző segítségével a Microsoft Azure-portálon

- Győződjön meg arról, hogy fogadhat válaszok a végpontok. Adja meg a végpont URL-címek egyikét a böngészőbe. Ha csatlakoztat, egy InvalidQueryParameterValue vagy hasonló XML-válasz kell kapnia.

- Ha a proxykiszolgáló valaki más is használja a Tártallózó, győződjön meg arról, hogy csatlakozhassanak. Ha a csatlakozás, előfordulhat, hogy kapcsolatba a proxy server rendszergazdával.

### <a name="tools-for-diagnosing-issues"></a>A problémák diagnosztizálásával eszközök

Ha hálózati eszközök, például a Fiddler a Windows, esetleg a problémák diagnosztizálásához az alábbiak szerint:

- Ha a proxyn keresztül történő működéséhez, előfordulhat, hogy a hálózati eszköz csatlakozni a proxyn keresztül történő konfigurálásához.

- Ellenőrizze a hálózati eszköz által használt port számát.

- Adja meg a helyi állomás URL-cím és a hálózati eszköz portszám Tártallózó proxybeállításai. Ebben az esetben megfelelően, ha a hálózati eszköz elindítja a hálózati kérelmek, felügyeleti és Szolgáltatásvégpontok Tártallózó által végzett naplózás. Írja be például a blob-végpontot egy böngészőben a https://cawablobgrs.blob.core.windows.net/, és le fogja kérni a válasz a következőhöz, ami alapján, az erőforrás létezik-e, bár nem férhet hozzá.

![kódminta](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Lépjen kapcsolatba a proxy-kiszolgálói rendszergazda

Ha a proxybeállításai megfelelőek, előfordulhat, hogy a proxy server rendszergazdától, és

- Győződjön meg arról, hogy a proxy blokkolja Azure felügyeleti vagy erőforrás-végpontok irányuló forgalmat.

- Ellenőrizze a proxy-kiszolgáló által használt hitelesítési protokoll. A Tártallózó jelenleg nem támogatja az NTLM-proxyk.

## <a name="unable-to-retrieve-children-error-message"></a>"Nem sikerült beolvasni a gyermekek" hibaüzenet jelenik meg

Ha proxyn keresztül csatlakoznak az Azure-ba, győződjön meg arról, hogy a proxybeállítások helyességéről. Ha az előfizetés vagy a fiók tulajdonosának a hozzáférési volt engedélyezni lehessen egy erőforrást, győződjön meg arról, olvasási, vagy erőforrás engedélyeinek listázása

### <a name="issues-with-sas-url"></a>SAS URL-cím problémái
Ha a szolgáltatás egy SAS URL-cím segítségével, és ezt a hibát tapasztaló csatlakozik:

- Győződjön meg arról, hogy az URL-cím biztosít-e olvasási és erőforrások sorolja fel a szükséges engedélyekkel.

- Győződjön meg arról, hogy az URL-cím nem járt le.

- Ha a hozzáférési házirendek az SAS URL-cím alapú, győződjön meg arról, hogy a házirend nincs visszavonva.

Ha véletlenül egy SAS URL-cím érvénytelen keresztül kapcsolódik, és nem választható le, kövesse az alábbi lépéseket:
1.  A Tártallózó futtatásakor nyomja le az F12 billentyűt a fejlesztői eszközök ablak megnyitásához.
2.  Kattintson az alkalmazás fülre, majd kattintson a helyi tároló > file:// a bal oldali fában.
3.  A szolgáltatás típusa a problematikus SAS URI-társított kulcs található. Ha a hibás SAS URI blob-tároló, keresse meg például a kulcs nevű `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  A kulcs értékének kell egy JSON-tömb. A hibás URI-azonosítóhoz tartozó objektum található, és távolítsa el.
5.  Töltse be újra a Tártallózó Ctrl + R billentyűkombinációt.

## <a name="linux-dependencies"></a>Linux-függőségek

A Linux disztribúciókkal eltérő Ubuntu 16.04 szükség lehet manuálisan kell telepítenie néhány függősége. Általában a következő csomagok szükségesek:
* libgconf-2-4
* libsecret
* Naprakész ÖET

Attól függően, hogy a distro lehet többi csomagot, telepítenie kell. A Tártallózó [kibocsátási megjegyzések](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) néhány disztribúciókkal az adott lépést tartalmaznak.

## <a name="next-steps"></a>További lépések

Ha a megoldások egyike sem működik, az Ön, küldje el a problémát a visszajelzés eszközzel együtt az e-maileket, és annyi információkhoz juthat a problémáról, néven akkor is, így elküldhetjük Önnek a probléma.

Ehhez kattintson **súgó** menüben, majd kattintson **visszajelzés küldése**.

![Visszajelzés](./media/storage-explorer-troubleshooting/4022503_en_1.png)
