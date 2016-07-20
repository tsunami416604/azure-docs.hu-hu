<properties 
    pageTitle="Mi az az Azure RemoteApp? | Microsoft Azure" 
    description="Ismerje meg, hogyan oszthat meg alkalmazásokat és erőforrásokat bármilyen eszközön az Azure RemoteAppon keresztül." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/18/2016" 
    ms.author="elizapo"/>

# Mi az az Azure RemoteApp?

Az Azure RemoteApp az Azure-ban biztosítja a helyszíni Microsoft RemoteApp program funkcióit a távoli asztali szolgáltatások támogatásával. Az Azure RemoteApp segítségével biztonságos távoli elérést biztosíthat az alkalmazásokhoz számos különböző felhasználói eszközről. Az Azure RemoteApp alapvetően nem állandó terminálkiszolgáló-munkameneteket üzemeltet a felhőben, amelyeket használhat és megoszthat a felhasználóival.

Az Azure RemoteApp segítségével szinte bármilyen eszközön megoszthat alkalmazásokat és erőforrásokat a felhasználókkal. Az alkalmazásokat mi üzemeltetjük a felhőben, ami azt jelenti, hogy mi gondoskodunk a hardverről és a méretezésről, hogy megfeleljenek a felhasználói igényeknek. Önnek mindössze fel kell töltenie a megosztani kívánt alkalmazásokat, majd utasítani a felhasználókat, hogy használják az alkalmazásokat. [A felhasználók megtarthatják saját eszközeiket](remoteapp-clients.md), miközben Ön mindent az Azure portálon keresztül kezelhet. Lehetősége van arra is, hogy vállalati hitelesítő adatait használja, így biztosíthatja az alkalmazások és az adatok biztonságát.

Az alábbiakban további információkat talál az Azure RemoteAppról, vagy ha máris meggyőztük, [próbálja ki most](https://azure.microsoft.com/services/remoteapp/).

Kérdései vannak az Azure RemoteAppról? Tekintse meg a [gyakori kérdéseket](remoteapp-faq.md).

Az Azure RemoteApp a [Microsoft virtuális asztali infrastruktúra](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx) része.

**Újdonság!** Szeretne többet megtudni az Azure RemoteAppról? Vagy készen áll arra, hogy méretezve ellenőrizze az Azure RemoteAppot? Csatlakozzon a hetente megtartott [szakértői válaszok webes szemináriumhoz](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## Azure RemoteApp-gyűjtemények
Kétféle [Azure RemoteApp-gyűjtemény](remoteapp-collections.md) létezik:


- A **felhőalapú gyűjtemény** a felhőben üzemelteti a programokat és tárolja az adatokat. A felhasználók a Microsoft-fiókjukkal, illetve az Azure Active Directory-val szinkronizált vagy összevont vállalati hitelesítő adataikkal jelentkezhetnek be és érhetik el az alkalmazásokat.

    Akkor válassza a felhőalapú gyűjteményt, ha a megosztani kívánt alkalmazásnak nincs szüksége kapcsolatra a vállalat magánhálózatában lévő erőforrásokhoz (például egy VPN-eszközön keresztül). Ha az alkalmazás az interneten, a OneDrive-ban vagy az Azure-ban található erőforrásokat használ, használhatja a felhőalapú gyűjteményt. Emellett ez hozható létre a leggyorsabban.

- A **hibrid gyűjtemény** az Azure felhőben üzemel és ott tárolja az adatokat, de lehetővé teszi a felhasználók számára, hogy elérjék a helyi hálózaton tárolt adatokat és erőforrásokat is. A felhasználók az Azure Active Directoryval szinkronizált vagy összevont vállalati hitelesítő adataikkal jelentkezhetnek be, és érhetik el az alkalmazásokat.

    Akkor válassza a hibrid gyűjteményt, ha szüksége van kapcsolatra a vállalat magánhálózatán lévő erőforrásokhoz. Ha például az alkalmazásnak hozzá kell férnie az alábbiak egyikéhez:

    - Az intraneten található fájlkiszolgálók
    - Quicken
    - Tűzfal mögött lévő adatbázisok

    Ez általában azon nagy méretű vállalatok számára hasznos, amelyeknek sok erőforrásuk található a magánhálózatukon, amelyek nem helyezhetők át a felhőbe.

A különböző gyűjteményekhez különböző lehetőségek érhetők el, beleértve a hálózatokat, ezért fel kell mérnie, hogy [melyik gyűjtemény](remoteapp-collections.md) a legjobb az Ön számára. 


### A gyűjtemény frissítése
A hibrid és a felhőalapú gyűjtemények közötti legfőbb különbségek egyike a szoftverfrissítések kezelésének módja. Egy felhőgyűjtemény esetében, amely az Office 365 ProPlus vagy az Office 2013 előre telepített rendszerképét használja, nem kell aggódnia a frissítések miatt. A szolgáltatás karbantartja magát, és folyamatosan telepíti az alkalmazásokra és az operációs rendszerre frissítéseit.

A hibrid gyűjtemények, valamint az egyéni sablonrendszerképet használó felhőalapú gyűjtemények esetében a rendszerkép és az alkalmazások karbantartása az Ön feladata. A tartományhoz csatlakoztatott rendszerképek esetében olyan eszközökkel vezérelheti a frissítéseket, mint a Windows Update, a Csoportházirend vagy a System Center.

Miután frissíti az egyéni sablon rendszerképet, fel kell töltenie az új rendszerképet az Azure-felhőbe, majd frissítenie kell a gyűjteményt, hogy az új rendszerképet használja. (Ezt az Azure RemoteApp **Quick Start** (Gyors üzembe helyezés) oldalán vagy az Irányítópulton végezheti el.)

További információk: [A gyűjtemény frissítése](remoteapp-update.md).

## Támogatott RemoteApp-ügyfelek
Az Azure RemoteApp a Windows és Windows RT rendszerekhez készült RemoteApp-ügyfélalkalmazásokon, valamint a Mac, iOS és Android rendszerekhez készült Microsoft távoli asztali alkalmazásokon támogatott. A felhasználók mobileszközeiken vagy számítógépeiken használhatják ezeket az alkalmazásokat az új Azure RemoteApp-programok eléréséhez.

Az ügyfelekkel kapcsolatos további információk: [Accessing your apps in Azure RemoteApp](remoteapp-clients.md) (Az alkalmazások elérése az Azure RemoteAppban).

## További lépések
Rajta! Próbálja ki! A következő cikkek segítenek az Azure RemoteApp első lépéseiben:

- [What kind of collection do you need for Azure RemoteApp? (Milyen típusú gyűjteményre van szüksége az Azure RemoteApphoz?)](remoteapp-collections.md)
- [Create an Azure RemoteApp image (Azure RemoteApp-rendszerkép létrehozása)](remoteapp-imageoptions.md)
- [How to create a cloud collection of Azure RemoteApp (Az Azure RemoteApp felhőalapú gyűjteményének létrehozása)](remoteapp-create-cloud-deployment.md)
- [How to create a hybrid collection of Azure RemoteApp (Az Azure RemoteApp hibrid gyűjteményének létrehozása)](remoteapp-create-hybrid-deployment.md)
- [Hogyan működik a licenckezelés az Azure RemoteAppban?](remoteapp-licensing.md)
- [Best practices for using Azure RemoteApp (Ajánlott eljárások az Azure RemoteApp használatához)](remoteapp-bestpractices.md)
- [Azure RemoteApp – gyakori kérdések](remoteapp-faq.md)
 

### Segítsen nekünk, hogy segítsünk 
Tudta, hogy a cikk értékelése és alább hozzászólások írása mellett magát a cikket is módosíthatja? Valami hiányzik? Valami nem működik? Írtam valami olyat, ami nem egyértelmű? Görgessen fel, és kattintson a **Szerkesztés a GitHubon** vagy a **Szerkesztés** elemre a módosítások végrehajtásához. Ezek hozzánk kerülnek jóváhagyásra, és miután jóváhagytuk őket, itt fognak megjelenni.


<!--HONumber=Jun16_HO2-->


