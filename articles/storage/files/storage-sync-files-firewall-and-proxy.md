---
title: Az Azure File Sync helyszíni tűzfal és proxy beállításai |} A Microsoft Docs
description: Az Azure File Sync helyszíni hálózati konfigurációja
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 11/26/2018
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 23bf70cd60639aec3ea7e8504dc3f6ebccd4923f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883589"
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

**Alkalmazás-specifikus proxybeállítások** kifejezetten a forgalmat az Azure File Sync proxy konfigurálását teszik lehetővé. Alkalmazás-specifikus proxybeállítások újabb vagy 4.0.1.0 ügynök verziója támogatott és az ügynök telepítése közben, vagy a Set-StorageSyncProxyConfiguration PowerShell-parancsmag használatával konfigurálható.

Alkalmazás-specifikus Proxybeállítások konfigurálása a PowerShell-parancsokat:
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Gépre kiterjedő proxy beállításai** transzparensek az Azure File Sync ügynök, a teljes kiszolgáló adatforgalmat a proxyn keresztül.

Gépre kiterjedő proxy beállításainak konfigurálásához kövesse az alábbi lépéseket: 

1. Konfigurálja a proxybeállításokat a .NET-alkalmazásokban 

  - Módosítsa a két fájlt:  
    C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
    C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

  - Adja hozzá a < system.net > szakasz a machine.config fájlok (alább a < system.serviceModel > szakaszban).  127.0.01:8888 módosítsa az IP-cím és a proxykiszolgáló portját. 
  ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
  ```

2. Állítsa be a WinHTTP-proxybeállítások 

  - Futtassa a következő parancsot egy rendszergazda jogú parancssort vagy a Powershellt, tekintse meg a meglévő proxy beállításai:   

    a Netsh winhttp show proxy

  - Futtassa a következő parancsot egy rendszergazda jogú parancssort vagy a PowerShell segítségével állítsa be a proxybeállítást a (127.0.01:8888 módosítsa az IP-cím és a proxykiszolgáló port):  

    a Netsh winhttp proxy 127.0.0.1:8888 beállítása

3. Indítsa újra a Storage Sync-ügynök szolgáltatást a PowerShell vagy egy rendszergazda jogú parancssorból a következő parancs futtatásával: 

      net stop filesyncsvc

      Megjegyzés: A Storage Sync-ügynök (filesyncsvc) szolgáltatást fogja automatikus indítás után leállt.

## <a name="firewall"></a>Tűzfal
Az egyik előző szakaszban említett port 443-as kell lennie kimenő megnyitásához. Az adatközpontban, a fiókiroda vagy a régiót a házirendek alapján, további korlátozása adott tartományokra porton keresztüli forgalmat kívánt vagy szükséges.

A következő táblázat ismerteti a szükséges tartományok kommunikációhoz:

| Szolgáltatás | Nyilvános felhőbeli végpont | Azure Government-végpont | Használat |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Bármely felhasználó hívás (mint például a PowerShell) az URL-címet, beleértve a kezdeti server regisztrációs hívás/keresztül halad. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Az Azure Resource Manager-hívás egy hitelesített felhasználó általi kell elvégezni. Sikeres, a felhasználók hitelesítéséhez az URL-cím szolgál ki. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Az Azure File Sync üzembe helyezésének részeként egy egyszerű szolgáltatást az előfizetéshez tartozó Azure Active Directoryban jön létre. Az URL-címet, amely használható. Ez egyszerű delegálása az Azure File Sync szolgáltatás jogokat minimális számú szolgál. A felhasználó az Azure File Sync kezdeti telepítés végrehajtása egy hitelesített felhasználó az előfizetés-tulajdonosi jogosultságokkal kell lennie. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Amikor a rendszer letölti a fájlt, majd a kiszolgáló hajt végre adott adatáthelyezés hatékonyabban Ha közvetlenül az Azure-fájlmegosztást a Storage-fiókban folytatott kommunikációra. A kiszolgáló rendelkezik egy SAS-kulcs, amely csak lehetővé teszi a célzott fájl megosztás eléréséhez. |
| **Azure File Sync** | &ast;.one.microsoft.com | &ast;.afs.azure.us | Kezdeti kiszolgálót a regisztrációt követően a kiszolgáló egy regionális az Azure File Sync szolgáltatás példány URL-címet kap az adott régióban. A kiszolgáló URL-címe segítségével hatékonyan és közvetlenül kommunikálni a példány, ahogy szinkronizál kezelése. |
| **A Microsoft nyilvános kulcsokra épülő infrastruktúra** | `https://www.microsoft.com/pki/mscorp`<br />http://ocsp.msocsp.com | `https://www.microsoft.com/pki/mscorp`<br />http://ocsp.msocsp.com | Az Azure File Sync ügynök telepítése után a nyilvános kulcsokra épülő infrastruktúra URL-cím segítségével kommunikálni az Azure File Sync szolgáltatás és az Azure-fájlmegosztás szükséges köztes tanúsítványok letöltése. Az OCSP URL-cím segítségével a tanúsítvány állapotának ellenőrzéséhez. |

> [!Important]
> Ha engedélyezi a forgalmat &ast;. one.microsoft.com, nem csak a szinkronizálási szolgáltatás forgalmát lehetőség a kiszolgálóról. Nincsenek altartományok alatt elérhető számos további Microsoft-szolgáltatások.

Ha &ast;. one.microsoft.com túl széleskörű, a kiszolgálói kommunikációhoz azáltal, hogy az Azure Files Sync szolgáltatás csak explicit regionális példányai kommunikációs korlátozhatja. Melyik példány kiválasztása attól függ, hogy a régióban a storage sync service üzembe helyezését, és a kiszolgáló regisztrálva. Az alábbi táblázat régió neve "elsődleges végpont URL-címe".

Az üzletmenet-folytonossági és vészhelyreállítási (BCDR) helyreállítási okok miatt előfordulhat, hogy megadta az Azure-fájlmegosztások globálisan redundáns (GRS) tárfiók található. Ha ez a helyzet, majd az Azure-fájlmegosztások feladatátvételt hajt végre a párosított régióra tartós regionális kimaradás esetén. Az Azure File Sync az azonos regionális párok tárolóként használ. Ezért GRS-tárfiókok használatakor, engedélyeznie kell, hogy a kiszolgálót, hogy a párosított régió beszélgethet Azure File Sync további URL-címet. Az alábbi táblázat a "Paired régió" meghívja. Emellett van egy traffic manager profil URL-CÍMÉT, valamint engedélyezni kell. Ez biztosítja a hálózati forgalom zökkenőmentesen újra átirányítható a párosított régióra feladatátvétel esetén, és az alábbi táblázatban az "Felderítési URL-címe" nevezzük.

| Felhő  | Régió | Elsődleges végpont URL-címe | Párosított régió | Felderítési URL-cím |
|--------|--------|----------------------|---------------|---------------|
| Nyilvános |Kelet-Ausztrália | https://kailani-aue.one.microsoft.com | Délkelet-Ausztrália | https://kailani-aue.one.microsoft.com |
| Nyilvános |Délkelet-Ausztrália | https://kailani-aus.one.microsoft.com | Kelet-Ausztrália | https://tm-kailani-aus.one.microsoft.com |
| Nyilvános | Közép-Kanada | https://kailani-cac.one.microsoft.com | Kelet-Kanada | https://tm-kailani-cac.one.microsoft.com |
| Nyilvános | Kelet-Kanada | https://kailani-cae.one.microsoft.com | Közép-Kanada | https://tm-kailani.cae.one.microsoft.com |
| Nyilvános | USA középső régiója | https://kailani-cus.one.microsoft.com | USA 2. keleti régiója | https://tm-kailani-cus.one.microsoft.com |
| Nyilvános | Kelet-Ázsia | https://kailani11.one.microsoft.com | Délkelet-Ázsia | https://tm-kailani11.one.microsoft.com |
| Nyilvános | USA keleti régiója | https://kailani1.one.microsoft.com | USA nyugati régiója | https://tm-kailani1.one.microsoft.com |
| Nyilvános | USA 2. keleti régiója | https://kailani-ess.one.microsoft.com | USA középső régiója | https://tm-kailani-ess.one.microsoft.com |
| Nyilvános | Észak-Európa | https://kailani7.one.microsoft.com | Nyugat-Európa | https://tm-kailani7.one.microsoft.com |
| Nyilvános | Délkelet-Ázsia | https://kailani10.one.microsoft.com | Kelet-Ázsia | https://tm-kailani10.one.microsoft.com |
| Nyilvános | Az Egyesült Királyság déli régiója | https://kailani-uks.one.microsoft.com | Az Egyesült Királyság nyugati régiója | https://tm-kailani-uks.one.microsoft.com |
| Nyilvános | Az Egyesült Királyság nyugati régiója | https://kailani-ukw.one.microsoft.com | Az Egyesült Királyság déli régiója | https://tm-kailani-ukw.one.microsoft.com |
| Nyilvános | Nyugat-Európa | https://kailani6.one.microsoft.com | Észak-Európa | https://tm-kailani6.one.microsoft.com |
| Nyilvános | USA nyugati régiója | https://kailani.one.microsoft.com | USA keleti régiója | https://tm-kailani.one.microsoft.com |
| Government | USA-beli államigazgatás – Arizona | https://usgovarizona01.afs.azure.us | USA-beli államigazgatás – Texas | https://tm-usgovarizona01.afs.azure.us |
| Government | USA-beli államigazgatás – Texas | https://usgovtexas01.afs.azure.us | USA-beli államigazgatás – Arizona | https://tm-usgovtexas01.afs.azure.us |

- Ha helyileg redundáns (LRS) vagy zóna redundáns (ZRS) storage-fiókokat használ, csak az "elsődleges végpont URL-címe" alatt felsorolt URL-cím engedélyeznie kell.

- Globálisan redundáns (GRS) tárfiókok használatakor engedélyezéséhez a három URL-címét.

**Példa** A társzinkronizálási szolgáltatás központi telepítése `"West US"` és regisztrálja azt a kiszolgálót. Az URL-címeket, hogy a kiszolgáló felé történő ebben az esetben a következők:

> - https://kailani.one.microsoft.com (elsődleges végpont: USA nyugati RÉGIÓJA)
> - https://kailani1.one.microsoft.com (feladatátvételi párosított régió: USA keleti RÉGIÓJA)
> - https://tm-kailani.one.microsoft.com (felderítési URL-címét az elsődleges régió)

## <a name="summary-and-risk-limitation"></a>Összegzés és kockázati korlátozás
A listák a jelen dokumentum korábbi tartalmazza az URL-címeket az Azure File Sync jelenleg kommunikál. Tűzfalak ezekből a tartományokból kimenő forgalmat engedélyező képesnek kell lennie. A Microsoft nagy hangsúlyt fektet a, hogy ez a lista frissítése.

Tartomány korlátozása tűzfalszabályok beállítása, lehet, hogy egy mértéket a biztonság növelése érdekében. A tűzfal-konfigurációk használata esetén az egyik kell vegye figyelembe, hogy az URL-címeket a rendszer hozzáadja, és még idővel változhatnak. Ez a cikk rendszeres időközönként ellenőrizze.

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
