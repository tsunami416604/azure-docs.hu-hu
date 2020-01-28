---
title: Helyszíni tűzfal és proxybeállítások Azure File Sync | Microsoft Docs
description: Helyszíni hálózati konfiguráció Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dcf6160c3650975431bf50fcf5bcba67f833a717
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750446"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Az Azure File Sync proxy- és tűzfalbeállításai
Azure File Sync összekapcsolja a helyszíni kiszolgálókat a Azure Fileshoz, és lehetővé teszi a többhelyes szinkronizálást és a felhőalapú rétegbeli funkciókat. Ennek megfelelően a helyszíni kiszolgálónak csatlakoznia kell az internethez. A rendszergazdának el kell döntenie, hogy melyik a legjobb elérési út ahhoz, hogy a kiszolgáló elérje az Azure Cloud Services szolgáltatást.

Ez a cikk betekintést nyújt a-kiszolgáló sikeres és biztonságos összekapcsolásához szükséges konkrét követelményekkel és lehetőségekkel Azure File Synchoz.

## <a name="overview"></a>Áttekintés
Azure File Sync a Windows-kiszolgáló, az Azure-fájlmegosztás és számos más Azure-szolgáltatás között összehangoló szolgáltatásként működik, hogy szinkronizálja az adatait a szinkronizálási csoportban leírtak szerint. Ahhoz, hogy a Azure File Sync megfelelően működjön, konfigurálnia kell a kiszolgálókat a következő Azure-szolgáltatásokkal való kommunikációhoz:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Hitelesítési szolgáltatások

> [!Note]  
> A Windows Serveren futó Azure File Sync ügynök minden olyan kérést kezdeményez a Cloud Serviceshez, amely csak a tűzfal perspektívából érkező kimenő forgalmat veszi figyelembe. <br /> Egyetlen Azure-szolgáltatás sem kezdeményez kapcsolódást a Azure File Sync ügynökhöz.

## <a name="ports"></a>Portok
Azure File Sync a fájl-és metaadatokat kizárólag HTTPS-en keresztül helyezi át, és az 443-es portot kell megnyitni kimenőként.
Ennek eredményeképpen minden forgalom titkosítva van.

## <a name="networks-and-special-connections-to-azure"></a>Hálózatok és speciális kapcsolódás az Azure-hoz
A Azure File Sync ügynökhöz nem tartoznak követelmények olyan speciális csatornákra vonatkozóan, mint a [ExpressRoute](../../expressroute/expressroute-introduction.md), stb.

Azure File Sync az Azure-ba való belépést lehetővé tevő bármely eszközön elérhetővé válik, és automatikusan alkalmazkodik a különböző hálózati jellemzőkre, például a sávszélességre, a késésre, valamint a felügyelet vezérlésére a finomhangoláshoz. Jelenleg nem minden szolgáltatás érhető el. Ha konkrét viselkedést szeretne beállítani, tudassa velünk [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670)keresztül.

## <a name="proxy"></a>Proxy
Azure File Sync támogatja az alkalmazás-specifikus és a számítógép-szintű proxybeállításokat.

Az **alkalmazásspecifikus proxybeállítások** lehetővé teszik a proxy konfigurációját kifejezetten Azure file Sync forgalom számára. Az alkalmazásspecifikus proxybeállítások az ügynök 4.0.1.0 vagy újabb verziója esetén támogatottak, és az ügynök telepítése vagy a set-StorageSyncProxyConfiguration PowerShell-parancsmag használatával konfigurálhatók.

PowerShell-parancsok az alkalmazás-specifikus proxybeállítások konfigurálásához:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
A **számítógép-szintű proxybeállítások** transzparensek a Azure file Sync ügynök számára, mivel a kiszolgáló teljes forgalma a proxyn keresztül irányítható.

A számítógép-szintű proxybeállítások konfigurálásához kövesse az alábbi lépéseket: 

1. Proxybeállítások konfigurálása .NET-alkalmazásokhoz 

   - Szerkessze ezt a két fájlt:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adja hozzá a < System. net > szakaszt a Machine. config fájlokban (a < System. serviceModel > szakaszban).  Módosítsa a 127.0.01:8888 a proxykiszolgáló IP-címére és portjára. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. A WinHTTP proxy beállításainak megadása 

   - Futtassa a következő parancsot egy rendszergazda jogú parancssorból vagy a PowerShellből a meglévő proxy beállítás megtekintéséhez:   

     netsh WinHTTP show proxy

   - Futtassa a következő parancsot egy rendszergazda jogú parancssorból vagy a PowerShellből a proxybeállítások beállításához (módosítsa a 127.0.01:8888 a proxykiszolgáló IP-címére és portjára):  

     netsh WinHTTP set proxy 127.0.0.1:8888

3. Indítsa újra a Storage Sync Agent szolgáltatást úgy, hogy futtatja a következő parancsot egy rendszergazda jogú parancssorból vagy a PowerShellből: 

      net stop filesyncsvc

      Megjegyzés: a Storage Sync Agent (filesyncsvc) szolgáltatás a leállítást követően automatikusan elindul.

## <a name="firewall"></a>Tűzfal
Ahogy az előző szakaszban is említettük, a 443-es portot kell megnyitni. Az adatközpontban, ágban vagy régióban lévő szabályzatok alapján szükség lehet arra, hogy a porton keresztüli adatforgalmat bizonyos tartományokra szűkítse.

A következő táblázat ismerteti a szükséges tartományokat a kommunikációhoz:

| Szolgáltatás | Nyilvános Felhőbeli végpont | Azure Government végpont | Használat |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Bármely felhasználói hívás (például a PowerShell) az ezen az URL-címen halad át, beleértve a kezdeti kiszolgáló regisztrációs hívását is. |
| **Azure Active Directory** | https://login.windows.net<br>https://login.microsoftonline.com | https://login.microsoftonline.us | A Azure Resource Manager hívásokat hitelesített felhasználó kell elvégezni. A sikeres végrehajtáshoz ezt az URL-címet használja a rendszer a felhasználói hitelesítéshez. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | A Azure File Sync üzembe helyezésének részeként létrejön egy egyszerű szolgáltatásnév az előfizetés Azure Active Directory. Ezt az URL-címet használja a rendszer. Ez a rendszerbiztonsági tag a Azure File Sync szolgáltatáshoz minimálisan szükséges jogok delegálására szolgál. Azure File Sync kezdeti beállítását végző felhasználónak hitelesített felhasználónak kell lennie az előfizetés tulajdonosának jogosultságokkal. |
| **Azure Storage** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | Amikor a kiszolgáló letölt egy fájlt, a kiszolgáló hatékonyabban hajtja végre az adatáthelyezést, amikor közvetlenül a Storage-fiókban lévő Azure-fájlmegosztás felé folytatja a kommunikációt. A kiszolgálónak van egy SAS-kulcsa, amely csak a célként megadott fájlmegosztás elérését teszi lehetővé. |
| **Azure File Sync** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | A kiszolgáló kezdeti regisztrációja után a kiszolgáló megkapja az adott régióban található Azure File Sync szolgáltatási példány regionális URL-címét. A kiszolgáló az URL-cím használatával közvetlenül és hatékonyan tud kommunikálni a szinkronizálást kezelő példánnyal. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Miután telepítette a Azure File Sync ügynököt, a PKI URL-címével letöltheti a Azure File Sync szolgáltatással és az Azure-fájlmegosztás használatával folytatott kommunikációhoz szükséges köztes tanúsítványokat. Az OCSP URL-cím segítségével ellenőrizhető a tanúsítvány állapota. |

> [!Important]
> Ha engedélyezi a forgalmat a &ast;. one.microsoft.com-ra, akkor a kiszolgálóról érkező forgalom több, mint a szinkronizálási szolgáltatásra van lehetséges. Számos további Microsoft-szolgáltatás érhető el az altartományok alatt.

Ha &ast;. one.microsoft.com túl széles, korlátozhatja a kiszolgáló kommunikációját, mivel lehetővé teszi a kommunikációt az Azure Files Sync szolgáltatás explicit regionális példányaira. A kiválasztható példány (ok) a Storage Sync szolgáltatás azon régiójától függ, amelyet üzembe helyezett, és amelybe regisztrálta a kiszolgálót. Ezt a régiót az alábbi táblázatban "elsődleges végpont URL-ként" nevezzük.

Az üzletmenet folytonossága és a vész-helyreállítás (BCDR) miatt előfordulhat, hogy az Azure-fájlmegosztást globálisan redundáns (GRS) Storage-fiókban adta meg. Ha ez az eset áll fenn, akkor az Azure-fájlmegosztás egy tartós regionális kimaradás esetén átveszi a feladatokat a párosított régióba. Azure File Sync ugyanazokat a regionális párosításokat használja, mint a Storage. Így ha GRS-fiókokat használ, engedélyeznie kell a további URL-címeket, hogy a kiszolgáló a párosított régióval beszéljen a Azure File Sync. Az alábbi táblázat ezt a "párosított régiót" hívja meg. Ezen kívül egy Traffic Manager-profil URL-címét is engedélyezni kell. Ez biztosítja, hogy a hálózati forgalom zökkenőmentesen átirányítható legyen a párosított régióba feladatátvétel esetén, és az alábbi táblázatban "felderítési URL-cím" néven jelenik meg.

| Felhőbeli  | Region (Régió) | Elsődleges végpont URL-címe | Párosított régió | Felderítési URL-cím |
|--------|--------|----------------------|---------------|---------------|
| Nyilvános |Ausztrália keleti régiója | https:\//kailani-aue.one.microsoft.com | Délkelet-Ausztrália | https:\//tm-kailani-aue.one.microsoft.com |
| Nyilvános |Délkelet-Ausztrália | https:\//kailani-aus.one.microsoft.com | Ausztrália keleti régiója | https:\//tm-kailani-aus.one.microsoft.com |
| Nyilvános | Dél-Brazília | https:\//brazilsouth01.afs.azure.net | USA déli középső régiója | https:\//tm-brazilsouth01.afs.azure.net |
| Nyilvános | Közép-Kanada | https:\//kailani-cac.one.microsoft.com | Kelet-Kanada | https:\//tm-kailani-cac.one.microsoft.com |
| Nyilvános | Kelet-Kanada | https:\//kailani-cae.one.microsoft.com | Közép-Kanada | https:\//tm-kailani.cae.one.microsoft.com |
| Nyilvános | Közép-India | https:\//kailani-cin.one.microsoft.com | Dél-India | https:\//tm-kailani-cin.one.microsoft.com |
| Nyilvános | USA középső régiója | https:\//kailani-cus.one.microsoft.com | USA 2. keleti régiója | https:\//tm-kailani-cus.one.microsoft.com |
| Nyilvános | Kelet-Ázsia | https:\//kailani11.one.microsoft.com | Délkelet-Ázsia | https:\//tm-kailani11.one.microsoft.com |
| Nyilvános | USA keleti régiója | https:\//kailani1.one.microsoft.com | USA nyugati régiója | https:\//tm-kailani1.one.microsoft.com |
| Nyilvános | USA 2. keleti régiója | https:\//kailani-ess.one.microsoft.com | USA középső régiója | https:\//tm-kailani-ess.one.microsoft.com |
| Nyilvános | Kelet-Japán | https:\//japaneast01.afs.azure.net | Nyugat-Japán | https:\//tm-japaneast01.afs.azure.net |
| Nyilvános | Nyugat-Japán | https:\//japanwest01.afs.azure.net | Kelet-Japán | https:\//tm-japanwest01.afs.azure.net |
| Nyilvános | Korea középső régiója | https:\//koreacentral01.afs.azure.net/ | Korea déli régiója | https:\//tm-koreacentral01.afs.azure.net/ |
| Nyilvános | Korea déli régiója | https:\//koreasouth01.afs.azure.net/ | Korea középső régiója | https:\//tm-koreasouth01.afs.azure.net/ |
| Nyilvános | USA északi középső régiója | https:\//northcentralus01.afs.azure.net | USA déli középső régiója | https:\//tm-northcentralus01.afs.azure.net |
| Nyilvános | Észak-Európa | https:\//kailani7.one.microsoft.com | Nyugat-Európa | https:\//tm-kailani7.one.microsoft.com |
| Nyilvános | USA déli középső régiója | https:\//southcentralus01.afs.azure.net | USA északi középső régiója | https:\//tm-southcentralus01.afs.azure.net |
| Nyilvános | Dél-India | https:\//kailani-sin.one.microsoft.com | Közép-India | https:\//tm-kailani-sin.one.microsoft.com |
| Nyilvános | Délkelet-Ázsia | https:\//kailani10.one.microsoft.com | Kelet-Ázsia | https:\//tm-kailani10.one.microsoft.com |
| Nyilvános | Egyesült Királyság déli régiója | https:\//kailani-uks.one.microsoft.com | Egyesült Királyság nyugati régiója | https:\//tm-kailani-uks.one.microsoft.com |
| Nyilvános | Egyesült Királyság nyugati régiója | https:\//kailani-ukw.one.microsoft.com | Egyesült Királyság déli régiója | https:\//tm-kailani-ukw.one.microsoft.com |
| Nyilvános | USA nyugati középső régiója | https:\//westcentralus01.afs.azure.net | USA 2. nyugati régiója | https:\//tm-westcentralus01.afs.azure.net |
| Nyilvános | Nyugat-Európa | https:\//kailani6.one.microsoft.com | Észak-Európa | https:\//tm-kailani6.one.microsoft.com |
| Nyilvános | USA nyugati régiója | https:\//kailani.one.microsoft.com | USA keleti régiója | https:\//tm-kailani.one.microsoft.com |
| Nyilvános | USA 2. nyugati régiója | https:\//westus201.afs.azure.net | USA nyugati középső régiója | https:\//tm-westus201.afs.azure.net |
| Közigazgatás | US Gov Arizona | https:\//usgovarizona01.afs.azure.us | US Gov Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Közigazgatás | US Gov Texas | https:\//usgovtexas01.afs.azure.us | US Gov Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Ha a helyileg redundáns (LRS) vagy a Zone redundáns (ZRS) Storage-fiókokat használja, csak engedélyeznie kell az "elsődleges végpont URL-címe" alatt felsorolt URL-címet.

- Ha globálisan redundáns (GRS) tárolási fiókokat használ, engedélyezze a három URL-címet.

**Példa:** A Storage Sync szolgáltatást `"West US"`-ben helyezi üzembe, és regisztrálja a kiszolgálót. Az URL-címek, amelyek lehetővé teszik, hogy a kiszolgáló kommunikáljon a következő esetekben:

> - https:\//kailani.one.microsoft.com (elsődleges végpont: USA nyugati régiója)
> - https:\//kailani1.one.microsoft.com (párosított feladatátvételi régió: USA keleti régiója)
> - https:\//tm-kailani.one.microsoft.com (az elsődleges régió felderítési URL-címe)

## <a name="test-network-connectivity-to-service-endpoints"></a>Hálózati kapcsolat tesztelése a szolgáltatási végpontokhoz
Ha egy kiszolgáló regisztrálva van a Azure File Sync szolgáltatásban, a test-StorageSyncNetworkConnectivity parancsmag és a ServerRegistration. exe segítségével tesztelheti a kiszolgálón található összes végponttal (URL-címmel) folytatott kommunikációt. Ez a parancsmag segít elhárítani a hiányos kommunikációt, ami megakadályozza, hogy a kiszolgáló teljes mértékben működjön a Azure File Sync, és használható a proxy és a tűzfal konfigurációinak finomhangolására.

A hálózati kapcsolat teszt futtatásához telepítse a Azure File Sync Agent 9,1-es vagy újabb verzióját, és futtassa a következő PowerShell-parancsokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Összefoglalás és kockázati korlátozás
A dokumentum korábbi listája tartalmazza azokat az URL-címeket, Azure File Sync jelenleg kommunikál. A tűzfalaknak képesnek kell lenniük a kimenő forgalom engedélyezésére ezen tartományokban. A Microsoft igyekszik megőrizni a listát.

A tartomány korlátozására vonatkozó tűzfalszabályok beállítása lehet egy mérték a biztonság növelése érdekében. Ha ezeket a tűzfal-konfigurációkat használja, az egyiknek figyelembe kell vennie, hogy az URL-címek fel lesznek véve, és akár idővel is változhatnak. Ebben a cikkben rendszeresen tájékozódhat.

## <a name="next-steps"></a>Következő lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
