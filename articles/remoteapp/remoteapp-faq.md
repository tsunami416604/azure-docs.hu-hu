---
title: "Azure RemoteApp – gyakori kérdések | Microsoft Docs"
description: "Megtudhatja a válaszokat az Azure RemoteApp szolgáltatással kapcsolatban leggyakrabban feltett kérdésekre."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: swadhwa
editor: 
ms.assetid: bad66603-91f9-437f-8a70-236405d2a27f
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: fcc53e4073a6957fae263cfb5d38023ed9710d21
ms.openlocfilehash: 31df73e3fc4142ba8c3634ac9d2b6fea4cc1b2d9


---
# <a name="azure-remoteapp-faq"></a>Azure RemoteApp – gyakori kérdések
> [!IMPORTANT]
> Azure RemoteApp hamarosan megszűnik. A részletekért olvassa el a [bejelentést](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

A következő kérdéseket hallottuk az Azure RemoteApp szolgáltatással kapcsolatban. További kérdései vannak? Látogasson el a [RemoteApp-fórumokra](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp), és tegye fel nekünk a kérdéseit, vagy szóljon hozzá alább.

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>Nem találja, amit keres? Kérdése van, amire nem kapott választ?
Ha nem találja a keresett információt, vagy olyan kérdése van, amelyre itt nem kapott választ, kérjük, keresse fel az [Azure RemoteApp fórumát](http://aka.ms/araforum), és tegye fel ott a kérdését. Itt bármikor közzétehetünk új válaszokat.

## <a name="what-is-azure-remoteapp"></a>Mi az az Azure RemoteApp?
* **Mi az az Azure RemoteApp?** A RemoteApp egy Azure-szolgáltatás, amely segítségével biztonságos távoli elérést biztosíthat az alkalmazásokhoz számos különböző felhasználói eszközről. Tudjon meg többet az [Azure RemoteApp](remoteapp-whatis.md) szolgáltatásról.
* **Mik azok az üzembe helyezési beállítások?** Kétféle RemoteApp-gyűjtemény létezik: felhőalapú és hibrid. Az, hogy Önnek melyikre van szüksége, több tényezőtől is függ, például attól, hogy szükség van-e tartományhoz csatlakozásra. A döntésről bővebben [itt](remoteapp-collections.md) olvashat.

## <a name="quick-tips-on-using-azure-remoteapp"></a>Gyors tippek az Azure RemoteApp használatához
* **Mennyi időm van a kapcsolat megszakításáig? Mennyi ideig lehetek tétlen, mielőtt a rendszer kiléptet?** 4 órán át. Ha Ön vagy egy felhasználója 4 órán keresztül tétlen marad, az Azure RemoteApp automatikusan kilépteti. A további alapértelmezett beállításokat itt találhatja meg: [Azure Subscription and Service Limits, Quotas, and Constraints](../azure-subscription-service-limits.md) (Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései).
* **Kipróbálhatom a szolgáltatást ingyenesen?** Igen. Létezik egy ingyenes próbaverzió, amely 30 napig érhető el. Ha a próbaidő lejárt, dönthet, hogy átvált egy fizetős fiókra (amelyet éles környezetben használhat), vagy abbahagyja a szolgáltatás használatát. Az ingyenes próbaverzió használatának megkezdéséhez lépjen a [portal.azure.com](http://portal.azure.com) webhelyre, és hozzon létre egy új RemoteApp-példányt. Az ingyenes próbaverzióval két RemoteApp-példányt hozhat létre, példányonként 10 felhasználóval. Ne feledje, hogy a próbaverzió csak 30 napig használható.
  
  ## <a name="azure-remoteapp-subscription-details"></a>Az Azure RemoteApp-előfizetés részletei
* **Milyen korlátozások vannak érvényben a szolgáltatásra vonatkozóan?** Az Azure RemoteApp alapértelmezett beállításait és szolgáltatási korlátait itt ismerheti meg: [Azure Subscription and Service Limits, Quotas, and Constraints](../azure-subscription-service-limits.md) (Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései). Ha további kérdései vannak, ossza meg velünk.
* **Hány felhasználómnak kell lennie?** A felhasználók minimális száma 20. Csak az egyértelműség kedvéért: a 20 a MINIMUM. A számlázás 20 felhasználó után történik. 
* **Mennyibe kerül a RemoteApp szolgáltatás?** Tekintse meg az [Azure RemoteApp díjszabásának részleteit](https://azure.microsoft.com/pricing/details/remoteapp/).
* **Van olyan gyűjteménytípus, amely drágább, mint a többi?** Igen, előfordulhat, hogy van. Ez a gyűjteménnyel szemben támasztott követelményektől függ. A hibrid gyűjteményhez az Azure RemoteApp és az Ön helyszíni hálózata közötti kapcsolatra van szükség. Ha meglévő VNET-/Express Route-kapcsolatokat használ, ez nem jelent többletköltséget. De ha egy új Azure VNET-kapcsolatot, és egy átjárót vagy Express Route-kapcsolatot használ, a [VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway) vagy az [Express Route](https://azure.microsoft.com/pricing/details/expressroute/) használata díjköteles. Ez a költség (amelynek részleteit a hivatkozások között találja) az Azure RemoteApp szolgáltatás havi költségén felül fizetendő.

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>Gyűjtemények – mi támogatott, mit érdemes használni és egyebek
* **Támogatottak az egyéni üzletági (LOB) alkalmazások?** Igen. Ha egy egyéni alkalmazást szeretne használni az Azure RemoteApp szolgáltatásban, hozzon létre egy [egyéni sablonrendszerképet](remoteapp-create-custom-image.md), és töltse fel azt a RemoteApp-gyűjteménybe.
* **Működni fog az egyéni üzletági alkalmazásom az Azure RemoteAppben?** Ezt a legkönnyebben úgy tudja kideríteni, ha kipróbálja. Látogasson el a [Távoli asztali szolgáltatások kompatibilitási központjába](http://www.rdcompatibility.com/compatibility/default.aspx).
* **Melyik üzembe helyezési módszer (felhőalapú vagy hibrid) a legjobb a szervezetem számára?** A hibrid gyűjtemények nyújtják a legteljesebb körű élményt, ha egyszeri bejelentkezést (SSO), teljes integrációt és biztonságos helyszíni hálózati kapcsolódási lehetőséget szeretne használni. A felhőalapú gyűjtemények gyors és egyszerű módot nyújtanak az üzemelő példány elszigetelésére többféle hitelesítési módszer használatával. További információk az [üzembe helyezési lehetőségekről](remoteapp-whatis.md).
* **Van egy SQL- vagy más adatbázisunk a helyszínen vagy az Azure-ban. Melyik üzembe helyezési típust érdemes használnunk?** Ez attól függ, hogy az SQL- vagy háttéradatbázis hol található. Ha az adatbázis egy magánhálózaton található, a hibrid gyűjteményt érdemes használni. Ha az adatbázis közvetlenül az internetre csatlakozik, és ügyfelek csatlakozhatnak hozzá, a felhőalapú gyűjtemény használható.
* **Mi a helyzet a meghajtó-hozzárendeléssel, az USB- és a soros portokkal, a vágólapmegosztással és a nyomtatóátirányítással?** Az Azure RemoteApp ezek mindegyikét támogatja. A vágólapmegosztás és a nyomtatóátirányítás alapértelmezés szerint engedélyezve van. Az átirányításról [itt](remoteapp-redirection.md) tudhat meg többet. 

## <a name="template-images"></a>Sablonrendszerképek
* **Használhatok egy felhőt vagy egy meglévő virtuális gépet sablonként a RemoteApp-gyűjteményemhez?** Igen! Létrehozhat egy Azure virtuális gépen alapuló rendszerképet, használhatja az előfizetéséhez járó rendszerképeket, vagy létrehozhat egyéni rendszerképeket. További információkért lásd: [RemoteApp-rendszerképekkel kapcsolatos lehetőségek](remoteapp-imageoptions.md).

## <a name="network-options"></a>Hálózati lehetőségek
* **A hibrid gyűjteménynek VNET-kapcsolatra van szüksége. Használhatjuk a meglévő VNET-kapcsolatunkat?** Igen, ha a meglévő VNET egy Azure VNET. További információkért lásd a [hibrid gyűjteményekkel kapcsolatos útmutatás](remoteapp-create-hybrid-deployment.md) 1. lépését: „A virtuális hálózat beállítása”.
* **Használhatok VNET-kapcsolatot felhőalapú gyűjtemény esetében?** Természetesen igen. További információért lásd: [Felhőalapú gyűjtemény létrehozása](remoteapp-create-cloud-deployment.md), különösen az 1. lépést.

## <a name="authentication-options"></a>Hitelesítési lehetőségek
* **Mi a helyzet a hitelesítéssel? Milyen módszerek támogatottak?** A felhőalapú gyűjtemény támogatja a Microsoft-fiókokat és az Azure Active Directory-fiókokat, amelyek egyben Office 365-fiókok is. A hibrid gyűjtemény csak az olyan Azure Active Directory-fiókokat támogatja, amelyek a Windows Server Active Directory üzemelő példányából lettek szinkronizálva (az [Azure Active Directory-szinkronizáló](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx) vagy hasonló eszköz használatával); pontosabban vagy a jelszó-szinkronizálási lehetőséggel, vagy az Active Directory összevonási szolgáltatások (AD FS) összevonásának konfigurálásával lettek szinkronizálva. Ezenkívül konfigurálhatja a [Multi-Factor Authentication (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/) szolgáltatást is.

> [!NOTE]
> Az Azure Active Directory-felhasználóknak ugyanattól a bérlőtől kell származniuk, amely az Ön előfizetéséhez rendelve van. (Az előfizetését a portál **Beállítások** lapján tekintheti meg és módosíthatja. További információk: [A RemoteApp által használt Azure Active Directory-bérlő módosítása](remoteapp-changetenant.md).)
> 
> 

* **Miért nem tudok hozzáférést biztosítani Azure Active Directory-fiókom számára?** Az Azure Active Directory-felhasználóknak ugyanabból a könyvtárból kell származniuk, amely az Ön előfizetéséhez rendelve van. A könyvtárat a portál Beállítások lapján tekintheti meg és módosíthatja. További információk: [A RemoteApp által használt Azure Active Directory-bérlő módosítása](remoteapp-changetenant.md).

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>Ügyfelek – mely eszközökről érhetem el az Azure RemoteAppet?
Az ügyfelekkel kapcsolatos információkat, többek között a különböző ügyfelek telepítési lépéseit itt találhatja meg: [Accessing your apps in Azure RemoteApp](remoteapp-clients.md) (Az alkalmazások elérése az Azure RemoteAppben).

* **Mely eszközöket és operációs rendszereket támogatják az ügyfélalkalmazások?**
  Számítógépek és táblagépek: 
  
  * Windows 10 (ügyfél előzetes kiadása)
  * Windows 8.1 és Windows 8
  * Windows 7 Service Pack 1
  * Mac OS X
  * Windows RT
  * Android rendszerű táblagépek
  * iPadek és telefonok:
  * iPhone
  * Android rendszerű telefonok
  * Windows Phone
    
    [Töltsön le](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) egy RemoteApp-ügyfelet most.
* **Támogatja az Azure RemoteApp a vékony ügyfeleket?** Igen, a következő Windows Embedded vékony ügyfelek támogatottak:
  
  * Windows Embedded Standard 7
  * Windows Embedded 8 Standard
  * Windows Embedded 8.1 Industry Pro
  * Windows 10 IoT Enterprise
* **Melyik Windows Server-verziót támogatja a Távoli asztali munkamenetgazda (RDSH)?** Windows Server 2012 R2.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés
* **Mi található a RemoteApp támogatási csomagjában?** A számlázási és előfizetés-kezelési támogatás ingyenesen elérhető. Műszaki támogatás [Azure támogatási csomagok](https://azure.microsoft.com/support/plans/) formájában érhető el. Emellett [Azure vitafórumunkban](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp) ingyenes közösségi támogatáshoz juthat. 
* **Hogyan küldhetek visszajelzést?** Látogasson el a [visszajelzési fórumra](https://feedback.azure.com/forums/247748-azure-remoteapp/).
* **Kitől tudhatok meg többet az Azure RemoteApp szolgáltatásról?** Kérdéseit felteheti a [vitafórumon](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), vagy csatlakozhat a heti rendszerességű [szakértői válaszok webes szemináriumhoz](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), ahol mindenről szó van a RemoteApp szolgáltatással kapcsolatban.
* **Mi a helyzet a RemoteApp dokumentációjával?** Örülünk, hogy megkérdezte. A portál súgófiókjában található súgótartalmakon kívül (ezek eléréséhez egyszerűen kattintson a **?** elemre a portál bármely lapján) a következő cikkekből mindent megtudhat a RemoteApp szolgáltatásról:
  
  * **Első lépések:**
    * [Mi az a RemoteApp?](remoteapp-whatis.md)
    * [Mit tartalmaznak a RemoteApp sablon rendszerképei?](remoteapp-images.md)
    * [Hogyan működik a licenckezelés?](remoteapp-licensing.md)
    * [Hogyan működik együtt a RemoteApp és az Office?](remoteapp-o365.md)
    * [Hogyan működik az átirányítás a RemoteApp szolgáltatásban](remoteapp-redirection.md)?
  * **Üzembe helyezés:**
    * [Egyéni sablonrendszerkép létrehozása](remoteapp-create-custom-image.md)
    * [Hibrid gyűjtemény létrehozása](remoteapp-create-hybrid-deployment.md)
    * [Felhőalapú gyűjtemény létrehozása](remoteapp-create-cloud-deployment.md)
    * [Az Azure Active Directory konfigurálása a RemoteApp szolgáltatáshoz](remoteapp-ad.md)
    * [Alkalmazás közzététele a RemoteApp szolgáltatásban](remoteapp-publish.md)
  * **Kezelés:**
    
    * [Felhasználók hozzáadása](remoteapp-user.md)
    * [Ajánlott eljárások a RemoteApp konfigurálásához és használatához](remoteapp-bestpractices.md)    
    
    Videók: A RemoteApp szolgáltatásról számos videónk érhető el. Ezek némelyike bevezető jellegű ([Ismerkedés az Azure RemoteApp szolgáltatással](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)), míg mások az üzembe helyezésben nyújtanak segítséget ([Felhőalapú üzemelő példány](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) és [Hibrid üzemelő példány](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Nézze meg őket!

### <a name="help-us-help-you"></a>Segítsen nekünk, hogy segítsünk
Tudta, hogy a cikk értékelése és alább hozzászólások írása mellett magát a cikket is módosíthatja? Valami hiányzik? Valami nem működik? Írtam valami olyat, ami nem egyértelmű? Görgessen fel, és kattintson a **Szerkesztés a GitHubon** elemre a módosítások végrehajtásához. Ezek hozzánk kerülnek jóváhagyásra, és miután jóváhagytuk őket, itt fognak megjelenni.




<!--HONumber=Dec16_HO1-->


