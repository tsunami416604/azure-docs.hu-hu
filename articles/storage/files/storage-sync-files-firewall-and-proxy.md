---
title: Az Azure File Sync helyszíni tűzfal és proxy beállításai |} A Microsoft Docs
description: Az Azure File Sync helyszíni hálózati konfigurációja
services: storage
documentationcenter: ''
author: fauhse
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 7d86082abb6412072af44a6b2d794bcf536fa18d
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342726"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Az Azure File Sync proxy- és tűzfalbeállításai
Az Azure File Sync kapcsolódik a helyszíni kiszolgálók az Azure Files többhelyes szinkronizálás és a felhőbeli rétegezés szolgáltatások engedélyezése. Ezért egy helyszíni kiszolgálón kapcsolódnia kell az internethez. Egy rendszergazdának kell döntenie, hogy a legjobb útvonalat a közvetítőn keresztül az Azure cloud services-kiszolgáló.

Ez a cikk konkrét követelmények, valamint lehetőség legyen elérhető a sikeres és biztonságos módon csatlakoztatni a kiszolgáló Azure File Sync betekintést biztosít.

> [!Important]
> Az Azure File Sync még nem támogatja tűzfalak és virtuális hálózatok tárfiókok esetében. 

## <a name="overview"></a>Áttekintés
Az Azure File Sync-tárolószervező szolgáltatást, a Windows Server, az Azure-fájlmegosztást és számos egyéb Azure-szolgáltatások szinkronizálja az adatokat, a szinkronizálási csoport leírtak szerint között funkcionál. Az Azure File Sync megfelelően működjön a kiszolgálók közötti kommunikációt az alábbi Azure-szolgáltatások konfigurálásához lesz szüksége:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Hitelesítési szolgáltatások

> [!Note]  
> Az Azure File Sync ügynök Windows Server összes kérelem a felhőszolgáltatásokhoz, ami csak kell figyelembe venni a kimenő forgalmat a tűzfal szempontjából kellene indítja el. <br /> Nem Azure-szolgáltatás az Azure File Sync ügynök kapcsolatot kezdeményez.


## <a name="ports"></a>Portok
Az Azure File Sync helyezi át a fájlok adatai és metaadatai kizárólag HTTPS protokollon keresztül, és megköveteli a 443-as porton a kimenő kell megnyitni.
Ennek eredményeképpen az összes forgalom titkosítva lesz.

## <a name="networks-and-special-connections-to-azure"></a>Hálózatok és a speciális kapcsolatok az Azure-bA
Az Azure File Sync ügynök nem tartoznak követelmények vonatkozó speciális csatornákat, például [ExpressRoute](../../expressroute/expressroute-introduction.md), stb. az Azure-bA.

Az Azure File Sync minden rendelkezésre álló eszközöket, amelyek lehetővé teszik az Azure-ba, automatikusan gyakorlatainak, például a sávszélesség, késés különböző hálózati jellemzők, valamint a felügyeleti vezérlés a finomhangolásra ajánlat reach keresztül fog működni. Nem minden funkciója jelenleg érhető el. Ha azt szeretné, hogy adott viselkedést, ossza meg velünk keresztül [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Az Azure File Sync támogatja az alkalmazásspecifikus és gépre kiterjedő proxy beállításai.

Gépre kiterjedő proxy beállításai az Azure File Sync ügynök átlátható, mert a teljes kiszolgáló adatforgalmat a proxyn keresztül.

Alkalmazás-specifikus proxy-beállítások lehetővé teszik a kifejezetten az Azure File Sync-forgalmat a proxy konfigurációját. Alkalmazás-specifikus proxybeállításokat a ügynökverzió 3.0.12.0 vagy újabb és az ügynök telepítése közben, vagy a Set-StorageSyncProxyConfiguration PowerShell-parancsmag használatával konfigurálható.

Alkalmazás-specifikus Proxybeállítások konfigurálása a PowerShell-parancsokat:
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```

## <a name="firewall"></a>Tűzfal
Az egyik előző szakaszban említett port 443-as kell lennie kimenő megnyitásához. Az adatközpontban, a fiókiroda vagy a régiót a házirendek alapján, további korlátozása adott tartományokra porton keresztüli forgalmat kívánt vagy szükséges.

A következő táblázat ismerteti a szükséges tartományok kommunikációhoz:

| Szolgáltatás | Tartomány | Használat |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Bármely felhasználó hívás (mint például a PowerShell) az URL-címet, beleértve a kezdeti server regisztrációs hívás/keresztül halad. |
| **Azure Active Directory** | https://login.windows.net | Az Azure Resource Manager-hívás egy hitelesített felhasználó általi kell elvégezni. Sikeres, a felhasználók hitelesítéséhez az URL-cím szolgál ki. |
| **Azure Active Directory** | https://graph.windows.net/ | Az Azure File Sync üzembe helyezésének részeként egy egyszerű szolgáltatást az előfizetéshez tartozó Azure Active Directoryban jön létre. Az URL-címet, amely használható. Ez egyszerű delegálása az Azure File Sync szolgáltatás jogokat minimális számú szolgál. A felhasználó az Azure File Sync kezdeti telepítés végrehajtása egy hitelesített felhasználó az előfizetés-tulajdonosi jogosultságokkal kell lennie. |
| **Azure Storage** | &ast;.core.windows.net | Amikor a rendszer letölti a fájlt, majd a kiszolgáló hajt végre adott adatáthelyezés hatékonyabban Ha közvetlenül az Azure-fájlmegosztást a Storage-fiókban folytatott kommunikációra. A kiszolgáló rendelkezik egy SAS-kulcs, amely csak lehetővé teszi a célzott fájl megosztás eléréséhez. |
| **Azure File Sync** | &ast;.one.microsoft.com | Kezdeti kiszolgálót a regisztrációt követően a kiszolgáló egy regionális az Azure File Sync szolgáltatás példány URL-címet kap az adott régióban. A kiszolgáló URL-címe segítségével hatékonyan és közvetlenül kommunikálni a példány, ahogy szinkronizál kezelése. |

> [!Important]
> Ha engedélyezi a forgalmat &ast;. one.microsoft.com, nem csak a szinkronizálási szolgáltatás forgalmát lehetőség a kiszolgálóról. Nincsenek altartományok alatt elérhető számos további Microsoft-szolgáltatások.

Ha &ast;. one.microsoft.com túl széleskörű, a kiszolgálói kommunikációhoz azáltal, hogy csak explicit regionális példányok az Azure Files Sync szolgáltatás korlátozhatja. Válassza ki, melyik példány attól függ, hogy a régióban a Storage Sync Service, amelyhez telepítését és a kiszolgáló regisztrálva. Ez a régió, a kiszolgáló engedélyeznie kell. Hamarosan lesz ahhoz, hogy új üzletmenet-folytonossági funkciókat több URL-címeket. 

| Régió | Az Azure File Sync regionális végpont URL-címe |
|--------|---------------------------------------|
| Kelet-Ausztrália | https://kailani-aue.one.microsoft.com |
| Közép-Kanada | https://kailani-cac.one.microsoft.com |
| USA keleti régiója | https://kailani1.one.microsoft.com |
| Délkelet-Ázsia | https://kailani10.one.microsoft.com |
| Az Egyesült Királyság déli régiója | https://kailani-uks.one.microsoft.com |
| Nyugat-Európa | https://kailani6.one.microsoft.com |
| USA nyugati régiója | https://kailani.one.microsoft.com |

> [!Important]
> Ezek részletes tűzfalszabályok határozza meg, ha ez a dokumentum gyakran ellenőrizni, és a tűzfalszabályok, mert hiányos vagy elavult URL-cím listaelemek a tűzfal beállításait a szolgáltatáskiesések elkerülése érdekében frissítse.

## <a name="summary-and-risk-limitation"></a>Összegzés és kockázati korlátozás
A listák a jelen dokumentum korábbi tartalmazza az URL-címeket az Azure File Sync jelenleg kommunikál. Tűzfalak ezeket a tartományokat, valamint a válaszok tőlük kimenő forgalmat engedélyező képesnek kell lennie. A Microsoft nagy hangsúlyt fektet a, hogy ez a lista frissítése.

Tartomány korlátozása tűzfalszabályok beállítása, lehet, hogy egy mértéket a biztonság növelése érdekében. A tűzfal-konfigurációk használata esetén az egyik kell vegye figyelembe, hogy URL-címek hozzáadása, változnak idővel. Ezért körültekintő mérték ellenőrzése ebben a dokumentumban a táblák egy változáskezelési folyamatot egy Azure File Sync ügynök verzióról a másikra a legújabb ügynököt egy tesztelési üzembe helyezés részeként. Így biztosítható, hogy a tűzfal van-e konfigurálva a legújabb ügynök tartományok forgalom engedélyezésére van szükség.

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync (előzetes verzió) üzembe helyezése](storage-sync-files-deployment-guide.md)
