---
title: Helyszíni tűzfal és proxybeállítások Azure File Sync | Microsoft Docs
description: Helyszíni hálózati konfiguráció Azure File Sync
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 33d5be20682c8341932a2a0021ccda27583775bd
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335967"
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

      Megjegyzés: A Storage Sync Agent (filesyncsvc) szolgáltatás automatikusan elindul, ha leállt.

## <a name="firewall"></a>Tűzfal
Ahogy az előző szakaszban is említettük, a 443-es portot kell megnyitni. Az adatközpontban, ágban vagy régióban lévő szabályzatok alapján szükség lehet arra, hogy a porton keresztüli adatforgalmat bizonyos tartományokra szűkítse.

A következő táblázat ismerteti a szükséges tartományokat a kommunikációhoz:

| Szolgáltatás | Nyilvános Felhőbeli végpont | Azure Government végpont | Használat |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Bármely felhasználói hívás (például a PowerShell) az ezen az URL-címen halad át, beleértve a kezdeti kiszolgáló regisztrációs hívását is. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | A Azure Resource Manager hívásokat hitelesített felhasználó kell elvégezni. A sikeres végrehajtáshoz ezt az URL-címet használja a rendszer a felhasználói hitelesítéshez. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | A Azure File Sync üzembe helyezésének részeként létrejön egy egyszerű szolgáltatásnév az előfizetés Azure Active Directory. Ezt az URL-címet használja a rendszer. Ez a rendszerbiztonsági tag a Azure File Sync szolgáltatáshoz minimálisan szükséges jogok delegálására szolgál. Azure File Sync kezdeti beállítását végző felhasználónak hitelesített felhasználónak kell lennie az előfizetés tulajdonosának jogosultságokkal. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Amikor a kiszolgáló letölt egy fájlt, a kiszolgáló hatékonyabban hajtja végre az adatáthelyezést, amikor közvetlenül a Storage-fiókban lévő Azure-fájlmegosztás felé folytatja a kommunikációt. A kiszolgálónak van egy SAS-kulcsa, amely csak a célként megadott fájlmegosztás elérését teszi lehetővé. |
| **Azure File Sync** | &ast;.one.microsoft.com | &ast;. afs.azure.us | A kiszolgáló kezdeti regisztrációja után a kiszolgáló megkapja az adott régióban található Azure File Sync szolgáltatási példány regionális URL-címét. A kiszolgáló az URL-cím használatával közvetlenül és hatékonyan tud kommunikálni a szinkronizálást kezelő példánnyal. |
| **Microsoft PKI** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Miután telepítette a Azure File Sync ügynököt, a PKI URL-címével letöltheti a Azure File Sync szolgáltatással és az Azure-fájlmegosztás használatával folytatott kommunikációhoz szükséges köztes tanúsítványokat. Az OCSP URL-cím segítségével ellenőrizhető a tanúsítvány állapota. |

> [!Important]
> Ha a. One.microsoft.com &ast;-re irányuló forgalom lehetővé teszi, a-kiszolgálóról érkező forgalom több, mint a szinkronizálási szolgáltatásra van lehetséges. Számos további Microsoft-szolgáltatás érhető el az altartományok alatt.

Ha &ast;a. One.microsoft.com túl széles, korlátozhatja a kiszolgáló kommunikációját azáltal, hogy lehetővé teszi a kommunikációt az Azure Files Sync szolgáltatás explicit regionális példányaira. A kiválasztható példány (ok) a Storage Sync szolgáltatás azon régiójától függ, amelyet üzembe helyezett, és amelybe regisztrálta a kiszolgálót. Ezt a régiót az alábbi táblázatban "elsődleges végpont URL-ként" nevezzük.

Az üzletmenet folytonossága és a vész-helyreállítás (BCDR) miatt előfordulhat, hogy az Azure-fájlmegosztást globálisan redundáns (GRS) Storage-fiókban adta meg. Ha ez az eset áll fenn, akkor az Azure-fájlmegosztás egy tartós regionális kimaradás esetén átveszi a feladatokat a párosított régióba. Azure File Sync ugyanazokat a regionális párosításokat használja, mint a Storage. Így ha GRS-fiókokat használ, engedélyeznie kell a további URL-címeket, hogy a kiszolgáló a párosított régióval beszéljen a Azure File Sync. Az alábbi táblázat ezt a "párosított régiót" hívja meg. Ezen kívül egy Traffic Manager-profil URL-címét is engedélyezni kell. Ez biztosítja, hogy a hálózati forgalom zökkenőmentesen átirányítható legyen a párosított régióba feladatátvétel esetén, és az alábbi táblázatban "felderítési URL-cím" néven jelenik meg.

| Felhő  | Régió | Elsődleges végpont URL-címe | Párosított régió | Felderítési URL-cím |
|--------|--------|----------------------|---------------|---------------|
| Nyilvános |Kelet-Ausztrália | https:\//Kailani-Aue.One.microsoft.com | Délkelet-Ausztrália | https:\//TM-Kailani-Aue.One.microsoft.com |
| Nyilvános |Délkelet-Ausztrália | https:\//kailani-aus.one.microsoft.com | Kelet-Ausztrália | https:\//TM-Kailani-AUS.One.microsoft.com |
| Nyilvános | Dél-Brazília | https:\//brazilsouth01.AFS.Azure.net | USA déli középső régiója | https:\//TM-brazilsouth01.AFS.Azure.net |
| Nyilvános | Közép-Kanada | https:\//Kailani-CAC.One.microsoft.com | Kelet-Kanada | https:\//TM-Kailani-CAC.One.microsoft.com |
| Nyilvános | Kelet-Kanada | https:\//Kailani-CAE.One.microsoft.com | Közép-Kanada | https:\//TM-Kailani.CAE.One.microsoft.com |
| Nyilvános | Közép-India | https:\//Kailani-cin.One.microsoft.com | Dél-India | https:\//TM-Kailani-cin.One.microsoft.com |
| Nyilvános | USA középső régiója | https:\//Kailani-cus.One.microsoft.com | USA 2. keleti régiója | https:\//TM-Kailani-cus.One.microsoft.com |
| Nyilvános | Kelet-Ázsia | https:\//kailani11.One.microsoft.com | Délkelet-Ázsia | https:\//TM-kailani11.One.microsoft.com |
| Nyilvános | East US | https:\//kailani1.One.microsoft.com | USA nyugati régiója | https:\//TM-kailani1.One.microsoft.com |
| Nyilvános | USA 2. keleti régiója | https:\//Kailani-ESS.One.microsoft.com | USA középső régiója | https:\//TM-Kailani-ESS.One.microsoft.com |
| Nyilvános | Kelet-Japán | https:\//japaneast01.afs.azure.net | Nyugat-Japán | https:\//tm-japaneast01.afs.azure.net |
| Nyilvános | Nyugat-Japán | https:\//japanwest01.afs.azure.net | Kelet-Japán | https:\//tm-japanwest01.afs.azure.net |
| Nyilvános | Korea középső régiója | https:\//koreacentral01.AFS.Azure.net/ | Korea déli régiója | https:\//TM-koreacentral01.AFS.Azure.net/ |
| Nyilvános | Korea déli régiója | https:\//koreasouth01.AFS.Azure.net/ | Korea középső régiója | https:\//TM-koreasouth01.AFS.Azure.net/ |
| Nyilvános | USA északi középső régiója | https:\//northcentralus01.AFS.Azure.net | USA déli középső régiója | https:\//TM-northcentralus01.AFS.Azure.net |
| Nyilvános | Észak-Európa | https:\//kailani7.One.microsoft.com | Nyugat-Európa | https:\//TM-kailani7.One.microsoft.com |
| Nyilvános | USA déli középső régiója | https:\//southcentralus01.AFS.Azure.net | USA északi középső régiója | https:\//TM-southcentralus01.AFS.Azure.net |
| Nyilvános | Dél-India | https:\//Kailani-Sin.One.microsoft.com | Közép-India | https:\//TM-Kailani-Sin.One.microsoft.com |
| Nyilvános | Délkelet-Ázsia | https:\//kailani10.One.microsoft.com | Kelet-Ázsia | https:\//TM-kailani10.One.microsoft.com |
| Nyilvános | Az Egyesült Királyság déli régiója | https:\//Kailani-UKs.One.microsoft.com | Az Egyesült Királyság nyugati régiója | https:\//TM-Kailani-UKs.One.microsoft.com |
| Nyilvános | Az Egyesült Királyság nyugati régiója | https:\//Kailani-Ukw.One.microsoft.com | Az Egyesült Királyság déli régiója | https:\//TM-Kailani-Ukw.One.microsoft.com |
| Nyilvános | USA nyugati középső régiója | https:\//westcentralus01.afs.azure.net | USA nyugati régiója, 2. | https:\//tm-westcentralus01.afs.azure.net |
| Nyilvános | Nyugat-Európa | https:\//kailani6.One.microsoft.com | Észak-Európa | https:\//TM-kailani6.One.microsoft.com |
| Nyilvános | USA nyugati régiója | https:\//Kailani.One.microsoft.com | East US | https:\//TM-Kailani.One.microsoft.com |
| Nyilvános | USA nyugati régiója, 2. | https:\//westus201.afs.azure.net | USA nyugati középső régiója | https:\//tm-westus201.afs.azure.net |
| Államigazgatás | USA-beli államigazgatás – Arizona | https:\//usgovarizona01.AFS.Azure.us | USA-beli államigazgatás – Texas | https:\//TM-usgovarizona01.AFS.Azure.us |
| Államigazgatás | USA-beli államigazgatás – Texas | https:\//usgovtexas01.afs.azure.us | USA-beli államigazgatás – Arizona | https:\//TM-usgovtexas01.AFS.Azure.us |

- Ha a helyileg redundáns (LRS) vagy a Zone redundáns (ZRS) Storage-fiókokat használja, csak engedélyeznie kell az "elsődleges végpont URL-címe" alatt felsorolt URL-címet.

- Ha globálisan redundáns (GRS) tárolási fiókokat használ, engedélyezze a három URL-címet.

**Példa:** Üzembe helyezi a Storage Sync szolgáltatást `"West US"` a-ben, és regisztrálja azt a kiszolgálóval. Az URL-címek, amelyek lehetővé teszik, hogy a kiszolgáló kommunikáljon a következő esetekben:

> - https:\//Kailani.One.microsoft.com (elsődleges végpont: USA nyugati régiója)
> - https:\//kailani1.One.microsoft.com (párosított feladatátvételi régió: USA keleti régiója)
> - https:\//TM-Kailani.One.microsoft.com (az elsődleges régió felderítési URL-címe)

## <a name="summary-and-risk-limitation"></a>Összefoglalás és kockázati korlátozás
A dokumentum korábbi listája tartalmazza azokat az URL-címeket, Azure File Sync jelenleg kommunikál. A tűzfalaknak képesnek kell lenniük a kimenő forgalom engedélyezésére ezen tartományokban. A Microsoft igyekszik megőrizni a listát.

A tartomány korlátozására vonatkozó tűzfalszabályok beállítása lehet egy mérték a biztonság növelése érdekében. Ha ezeket a tűzfal-konfigurációkat használja, az egyiknek figyelembe kell vennie, hogy az URL-címek fel lesznek véve, és akár idővel is változhatnak. Ebben a cikkben rendszeresen tájékozódhat.

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
