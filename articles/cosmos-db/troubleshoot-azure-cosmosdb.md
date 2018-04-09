---
title: Azure Cosmos DB kezelése az Azure Storage Explorerben
description: Megismerheti, hogyan kezelhető az Azure Cosmos DB az Azure Storage Explorerben.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Az Azure Cosmos DB in Storage Explorer hibaelhárítási áttekintése

Az [Azure Cosmos DB in Storage Explorer](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) egy önálló alkalmazás, amely lehetővé teszi az Azure- és Sovereign-felhőkben üzemeltetett Azure Cosmos DB-fiókokhoz való csatlakozást Windows, macOS és Linux rendszerekről. Az alkalmazás az Azure Cosmos DB-entitások kezelését, az adatok módosítását, valamint a tárolt eljárások és triggerek frissítését is lehetővé teszi, és olyan Azure-entitásokat is biztosít, mint a tárolóblobok és üzenetsorok.

Ez az útmutató összefoglalja az Azure Cosmos DB in Storage Explorer leggyakoribb problémáinak megoldásait.

- [Bejelentkezési problémák](#Sign-in-issues)
  - [Önaláírt tanúsítványok a tanúsítványláncokban](#Self-signed-certificate-in-certificate-chain)
  - [Az előfizetéseket nem sikerül lekérni](#Unable-to-retrieve-subscriptions)
  - [A hitelesítési oldalt nem sikerül megjeleníteni](#Unable-to-see-the-authentication-page)
  - [Fiók eltávolítása sikertelen](#Cannot-remove-account)
- [Http/Https-proxyval kapcsolatos probléma](#Http/Https-proxy-issue)
- [A „Helyi és csatolt” csomópont alatti „Fejlesztés” csomópont problémája](#Development-node-under-Local-and-Attached-node-issue)
- [Nem sikerül csatolni az Azure Cosmos DB-fiókot a „Helyi és csatolt” csomópont alatt](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Azure Cosmos DB-csomópont kibontása sikertelen](#Expand-Azure-Cosmos-DB-node-error)
- [Következő lépések](#Next-steps)

<h2 id="Sign-in-issues">Bejelentkezési problémák</h2>

Mielőtt továbblépne, indítsa újra az alkalmazást, és ellenőrizze, hogy megoldható-e a probléma.

<h2 id="Self-signed-certificate-in-certificate-chain">Önaláírt tanúsítványok a tanúsítványláncokban</h2>

Ennek a hibának többféle oka is lehet, a leggyakoribbak a következők:

1. Ön egy „transzparens proxy” mögött van, ami azt jelenti, hogy valaki (például az informatikai részleg) elfogja a HTTPS-forgalmat, visszafejti azt, majd újra titkosítja egy önaláírt tanúsítvány használatával.

2. Ön olyan szoftvereket futtat, például egy víruskereső szoftvert, amely a fogadott HTTPS-üzenetekbe önaláírt SSL-tanúsítványokat szúr be.

Amikor a Storage Explorer egy ilyen „önaláírt tanúsítványt” észlel, onnantól nem tudja megállapítani, hogy a fogadott HTTPS-üzenetet módosították-e. Ha azonban rendelkezik az önaláírt tanúsítvány egy példányával, akkor beállíthatja, hogy a Storage Explorer megbízzon benne. Ha nem tudja biztosan, hogy ki szúrja be a tanúsítványt, akkor megpróbálhatja kideríteni az alábbi lépések végrehajtásával:

1. Telepítse az Open SSL-t.
     - [Windows:](https://slproweb.com/products/Win32OpenSSL.html) (bármelyik egyszerűsített verzió megfelel)
     - Mac és Linux: elvileg eleve mellékelve van az operációs rendszerhez.
2. Futtassa az Open SSL-t.
    - Windows: lépjen a telepítési könyvtárba, azon belül a **/bin/** mappába, majd kattintson duplán az **openssl.exe** fájlra.
    - Mac és Linux: futtassa az **openssl** parancsot egy terminálról.
3. Hajtsa végre az `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha nem tudja biztosan, melyik tanúsítvány önaláírt, akkor figyeljen arra, hogy melyiknél azonos a tárgy („s:”) és a kiállító („i:”).
5.  Ha talált önaláírt tanúsítványokat, másolja ki egyenként a tartalmukat a **---BEGIN CERTIFICATE---** sorral kezdve és az **---END CERTIFICATE---** sorral bezárólag egy-egy új .cer fájlba.
6.  Nyissa meg a Storage Explorert, majd lépjen a **Szerkesztés** > **SSL-tanúsítványok** > **Tanúsítványok importálása** menüpontra. A fájlkeresővel keresse meg és nyissa meg a létrehozott .cer-fájlokat.

Ha a fenti lépésekkel nem talál önaláírt tanúsítványokat, küldjön visszajelzést, hogy további segítséget kapjon.

<h2 id="Unable-to-retrieve-subscriptions">Az előfizetéseket nem sikerül lekérni</h2>

Ha nem tudja lekérni az előfizetéseit, miután sikeresen bejelentkezett:

- Ellenőrizze, hogy a fióknak van-e hozzáférése az előfizetésekhez. Ehhez jelentkezzen be az [Azure Portalra](http://portal.azure.com/).
- Ügyeljen arra, hogy a megfelelő környezettel jelentkezzen be ([Azure](http://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Germany](https://portal.microsoftazure.de/), [Azure US Government](http://portal.azure.us/) vagy Egyéni környezet/Azure Stack).
- Ha proxyt használ, ügyeljen arra, hogy a Storage Explorer-proxy megfelelően legyen beállítva.
- Próbálja meg eltávolítani és újra hozzáadni a fiókot.
- Próbálja meg azt, hogy törli a következő fájlokat a kezdőkönyvtárból (például: C:\Users\ContosoUser), majd újra hozzáadja a fiókot:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Tartsa figyelemmel, hogy a fejlesztői eszközök konzolján (F12) nem jelennek-e meg hibaüzenetek.

![konzol](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">A hitelesítési oldalt nem sikerül megjeleníteni</h2>  

Ha nem látja a hitelesítési oldalt:

- A kapcsolat sebességétől függően eltarthat egy kis ideig, hogy a bejelentkezési oldal betöltsön. Várjon legalább egy percet, mielőtt bezárná a hitelesítési párbeszédablakot.
- Ha proxyt használ, ügyeljen arra, hogy a Storage Explorer-proxy megfelelően legyen beállítva.
- Nyissa meg a fejlesztői konzolt az F12 billentyűvel. Figyeljen a fejlesztői konzol válaszaira, és keressen bennük valamilyen támpontot arra vonatkozóan, hogy miért nem működik a hitelesítés.

<h2 id="Cannot-remove-account">Fiók eltávolítása sikertelen</h2>

Ha nem tud eltávolítani egy fiókot, vagy ha az újrahitelesítési hivatkozás nem működik, a következőkkel próbálkozhat.

- Próbálja meg azt, hogy törli a következő fájlokat a kezdőkönyvtárból, majd újra hozzáadja a fiókot:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Ha szeretné eltávolítani az SAS-hez csatolt Storage-erőforrásokat, törölje a következőket:
  - Windows rendszeren az %AppData%/StorageExplorer mappát
  - Mac rendszeren a /Users/<felhasználó_neve/Library/Applicaiton SUpport/StorageExplorer mappát
  - Linux rendszeren a ~/.config/StorageExplorer mappát
  - Ha ezeket a fájlokat törli, **újra meg kell adnia minden hitelesítő adatát**.


<h2 id="Http/Https-proxy-issue">Http/Https-proxyval kapcsolatos probléma</h2>

A bal oldali fában nem listázhatók az Azure Cosmos DB-csomópontok, amikor http/https-proxyt konfigurálunk az ASE-ben. Ez egy ismert probléma, amelyet a következő kiadásra kijavítunk. Jelenleg áthidaló megoldásként használhatja az Azure Cosmos DB-adatkezelőt az Azure Portalon. 

<h2 id="Development-node-under-Local-and-Attached-node-issue">A „Helyi és csatolt” csomópont alatti „Fejlesztés” csomópont problémája</h2>

A rendszer nem reagál arra, ha a bal oldali fában a „Helyi és csatolt” csomópont alatti „Fejlesztés” csomópontra kattintunk.  Ennek így is kell lennie. Az Azure Cosmos DB helyi emulátora csak a következő kiadástól lesz támogatott.

![Fejlesztési csomópont](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">Nem sikerül csatolni az Azure Cosmos DB-fiókot a „Helyi és csatolt” csomópont alatt</h2>

Ha az alábbi hibát látja, amikor csatolni próbál egy Azure Cosmos DB-fiókot a „Helyi és csatolt” csomópont alatt, akkor ellenőrizze, hogy a használt kapcsolati karakterlánc megfelelő-e.

![Azure Cosmos DB csatolása a „Helyi és csatolt” csomópont alatt sikertelen](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Azure Cosmos DB-csomópont kibontása sikertelen</h2>

Az alábbi hibával találkozhat, amikor megpróbálja kibontani a bal oldali fa csomópontjait. 

![Kibontási hiba](./media/troubleshoot-cosmosdb/expand-error.png)

Próbálkozzon a következőkkel:

- Ellenőrizze, hogy az Azure Cosmos DB-fiók nem áll-e kiépítés alatt, és próbálkozzon újra, miután a fiók sikeresen létrejött.
- Ha a fiók a „gyorselérési” vagy a „Helyi és csatolt” csomópont alatt található, akkor ellenőrizze, hogy nem lett-e törölve. Ha törölve lett, akkor manuálisan kell eltávolítania a csomópontot.

<h2 id="Next-steps">További lépések</h2>

Ha a megoldások egyike sem működik, küldjön egy e-mailt az Azure Cosmos DB fejlesztőcsapatának ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) a probléma részleteivel, és megpróbáljuk megtalálni a megoldást.





