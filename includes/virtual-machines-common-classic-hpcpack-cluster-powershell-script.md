---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2df08968ad66bd330611b975c045c9e9c9b240aa
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735556"
---
Attól függően, a környezet és a lehetőségek a parancsfájl az összes a fürtinfrastruktúrát, beleértve az Azure virtual network, tárfiókok, felhőszolgáltatások, tartományvezérlő, helyi vagy távoli SQL-adatbázisok, fő csomópontot és további fürtcsomópontok hozhat létre. A parancsfájl azt is megteheti, már meglévő Azure-infrastruktúrát használják, és a csak a HPC-fürtcsomópontok létrehozása.

HPC Pack-fürt tervezésével kapcsolatos háttér-információkat lásd: a [termék értékelése és tervezése](https://technet.microsoft.com/library/jj899596.aspx) és [bevezetés](https://technet.microsoft.com/library/jj899590.aspx) a HPC Pack 2012 R2 TechNet-könyvtárban.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**: Előfizetés az Azure globális vagy Azure China szolgáltatásban is használhatja. Az előfizetési korlátok hatással száma és típusa, a fürtcsomópontok telepítheti. További információ: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../articles/azure-subscription-service-limits.md).
* **Az Azure PowerShell-lel 0.8.10 Windows ügyfélszámítógépen, vagy később telepített és konfigurált**: Lásd: [Azure PowerShell használatának első lépései](/powershell/azureps-cmdlets-docs) a telepítés menetéről és a lépéseket az Azure-előfizetéshez való kapcsolódáshoz.
* **HPC Pack IaaS telepítési szkripttel**: Töltse le és csomagolja ki a legújabb verzióját a szkriptet a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). A parancsfájl a verzió ellenőrzéséhez futtassa `New-HPCIaaSCluster.ps1 –Version`. Ez a cikk a parancsfájl 4.5.2-es alapul.
* **Konfigurációs parancsfájl**: Hozzon létre egy XML-fájlt, amely a parancsfájlhoz a konfigurálása a HPC-fürtöt. Információért és példákért tekintse meg későbbi részeiben ebben a cikkben és a fájl, amely a telepítési parancsfájl társul Manual.rtf.

## <a name="syntax"></a>Szintaxis
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> A szkript futtatása rendszergazdaként.
> 
> 

### <a name="parameters"></a>Paraméterek
* **ConfigFile**: Itt adhatja meg a HPC-fürtöt írja le a konfigurációs fájl elérési útja. Tekintse meg a konfigurációs fájl ebben a témakörben tájékozódhat, vagy a fájl a mappában Manual.rtf parancsfájlt tartalmazó.
* **AdminUserName**: Megadja a felhasználó nevét. Ha a parancsfájl létrehozza a tartomány erdő, ez lesz az összes virtuális gép helyi rendszergazdai felhasználónevét és a tartományi rendszergazda neve. Ha a tartomány erdő már létezik, ez határozza meg a tartományi felhasználót a HPC Pack telepítéséhez helyi rendszergazdai felhasználónév.
* **AdminPassword**: Adja meg a rendszergazdai jelszót. Ha nincs megadva a parancssorban, a parancsfájl kérni fogja, hogy a jelszót.
* **HPCImageName** (nem kötelező): Megadja a HPC Pack VM-rendszerkép neve, a HPC-fürt üzembe helyezéséhez használt. Egy Microsoft által biztosított HPC Pack-lemezképet az Azure Marketplace-en kell lennie. Ha nincs megadva (általában ajánlott), a szkriptet úgy dönt, a legutóbb közzétett [HPC Pack 2012 R2-lemezkép](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/). A Windows Server 2012 R2 Datacenter HPC Pack 2012 R2 Update 3 telepítve a legújabb kép alapján történik.
  
  > [!NOTE]
  > Üzembe helyezés sikertelen lesz, ha nem ad meg egy érvényes HPC Pack-rendszerképet.
  > 
  > 
* **Naplófájl** (nem kötelező): Itt adhatja meg a telepítési naplófájl elérési útja. Ha nincs megadva, a szkript létrehoz egy naplófájlt a parancsprogramot futtató számítógép temp könyvtárában.
* **Kényszerített** (nem kötelező): Letiltja az összes megerősítési kérések.
* **NoCleanOnFailure** (nem kötelező): Megadja, hogy a rendszer nem távolítja el az Azure virtuális gépek, amelyek sikeresen nem települnek. Ezek a virtuális gépek manuális eltávolítása a központi telepítés folytatja a parancsfájl ismételt futtatása előtt, vagy a telepítés sikertelen lehet.
* **PSSessionSkipCACheck** (optional): Minden felhőszolgáltatáshoz a szkript által üzembe helyezett virtuális gépek az Azure automatikusan létrejön egy önaláírt tanúsítványt, és a felhőben lévő összes virtuális gép használja ezt a tanúsítványt az alapértelmezett Windows Rendszerfelügyeleti (webszolgáltatások WinRM) tanúsítvánnyal. Ezek az Azure-beli virtuális gépeken HPC-szolgáltatások üzembe helyezéséhez alapértelmezés szerint a parancsfájl átmenetileg telepíti ezeket a tanúsítványokat a helyi számítógép\\megbízható legfelső szintű hitelesítésszolgáltatók tárolójába az ügyfélszámítógép le a "nem megbízható hitelesítésszolgáltató" biztonsági hiba parancsprogram végrehajtásakor. A tanúsítványok törlődnek, amikor befejezi a parancsfájl. Ha ez a paraméter van megadva, a tanúsítványok nincsenek telepítve az ügyfélszámítógép és a biztonsági figyelmeztetést le lesz tiltva.
  
  > [!IMPORTANT]
  > Ez a paraméter nem ajánlott éles környezetekben üzemelő példányok.
  > 
  > 

### <a name="example"></a>Példa
A következő példában létrehozunk egy HPC Pack-fürthöz, a konfigurációs fájl használatával *MyConfigFile.xml*, és adja meg a fürt telepítéséhez rendszergazdai hitelesítő adatait.

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Néhány fontos megjegyzés
* A parancsfájl feladat küldése a HPC Pack webes portálon vagy a HPC Pack REST API használatával engedélyezheti.
* A parancsfájl is igény szerint egyéni előtti és utáni konfigurációs parancsfájlok futtathatók a fő csomópontot Ha szeretne további szoftvereket telepíteni, vagy más beállítások konfigurálása.

## <a name="configuration-file"></a>Konfigurációs fájl
Az üzembe helyezési parancsfájl konfigurációs fájlja egy XML-fájlt. A sémafájl HPCIaaSClusterConfig.xsd a HPC Pack IaaS telepítési parancsfájl mappa van. **IaaSClusterConfig** van a konfigurációs fájlt, amely a fájl a telepítési parancsfájl mappában Manual.rtf részletesen ismertetjük a gyermekelemet tartalmaz a gyökéreleme.

