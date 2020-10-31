---
title: Rövid útmutató – JBoss Enterprise Application (EAP) Red Hat Enterprise Linux (RHEL) Azure-beli virtuális gépen és virtuálisgép-méretezési csoporton
description: Vállalati Java-alkalmazások üzembe helyezése Red Hat JBoss EAP használatával Azure RHEL virtuális gépen és virtuálisgép-méretezési csoporton keresztül.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135379"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Vállalati Java-alkalmazások üzembe helyezése az Azure-ban a JBoss EAP használatával Red Hat Enterprise Linux

Ezek a rövid útmutató-sablonok bemutatják, hogyan helyezhet üzembe a [JBoss EAP](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) -t az Azure Virtual Machines (VM) és a Virtual Machine Scale sets [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) használatával. A központi telepítés ellenőrzéséhez egy minta Java-alkalmazás lesz a központi telepítésben. A JBoss EAP egy nyílt forráskódú alkalmazáskiszolgáló platform. Nagyvállalati szintű biztonságot, méretezhetőséget és teljesítményt nyújt Java-alkalmazásai számára. A RHEL egy nyílt forráskódú operációs rendszer (OS) platform. Lehetővé teszi a meglévő alkalmazások méretezését és az új technológiák kiépítését minden környezetben. A JBoss EAP és a RHEL a vállalati Java-alkalmazások bármilyen környezetben történő létrehozásához, futtatásához, üzembe helyezéséhez és kezeléséhez szükségesek. Ez egy kiváló, nyílt forráskódú megoldás helyszíni, virtuális környezetekhez, valamint magán-, nyilvános vagy hibrid felhőkben.

## <a name="prerequisite"></a>Előfeltétel 

* Aktív előfizetéssel rendelkező Azure-fiók. Azure-előfizetés beszerzéséhez aktiválja az [Azure-krediteket a Visual Studio-előfizetőknek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) , vagy [hozzon létre ingyen egy fiókot](https://azure.microsoft.com/pricing/free-trial).

* JBoss EAP-telepítés – rendelkeznie kell egy Red Hat-fiókkal, amely a JBoss EAP-re vonatkozó RHSM-jogosultsággal rendelkezik. Ez a jogosultság lehetővé teszi a Red Hat tesztelt és hitelesített JBoss EAP verzió letöltését.  Ha nem rendelkezik EAP-jogosultsággal, a Kezdés előtt szerezzen be egy [JBoss EAP próbaverziós előfizetést](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) . Új Red Hat-előfizetés létrehozásához nyissa meg a [Red Hat Customer Portalt](https://access.redhat.com/) , és állítson be egy fiókot.
F
* [Azure Command-Line felület](https://docs.microsoft.com/cli/azure/overview).

* RHEL lehetőségek – válassza az utólagos elszámolású (TB) vagy a saját előfizetés (BYOS) lehetőséget. A BYOS-mel aktiválnia kell a [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold rendszerképet a gyors üzembe helyezési sablon telepítése előtt.

## <a name="java-ee--jakarata-ee-application-migration"></a>Java EE/Jakarata EE Application Migration

### <a name="migrate-to-jboss-eap"></a>Migrálás a JBoss EAP-re
A JBoss EAP 7,2 és a 7,3 a Java Enterprise Edition (Java EE) 8 és a Jakarta EE 8 specifikációjának hitelesített implementációja. A JBoss EAP előre konfigurált beállításokat biztosít olyan funkciókhoz, mint például a magas rendelkezésre állás (HA) fürtözés, az üzenetkezelés és az elosztott gyorsítótárazás. Azt is lehetővé teszi, hogy a felhasználók a JBoss EAP által biztosított különféle API-kkal és szolgáltatásokkal írják be, telepítsenek és futtassanak alkalmazásokat.  A JBoss EAP-vel kapcsolatos további információkért tekintse meg a [JBoss eap 7,2 bemutatása](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) vagy a [JBoss EAP 7,3 bemutatása](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)című témakört.

 #### <a name="applications-on-jboss-eap"></a>Alkalmazások a JBoss EAP-ben

* Webszolgáltatási alkalmazások – a webszolgáltatások szabványos módon biztosítják a különböző szoftveralkalmazások közötti együttműködés módját. Minden alkalmazás különböző platformokon és keretrendszereken futtatható. Ezek a webszolgáltatások megkönnyítik a belső és heterogén alrendszer kommunikációját. További tudnivalókért látogasson el a [webszolgáltatási alkalmazások fejlesztése az eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) vagy a [webszolgáltatási alkalmazások fejlesztése az EAP 7,3 szolgáltatásban](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)című témakörben.

* Enterprise Java Beans-(EJB-) alkalmazások – a EJB 3,2 egy olyan API, amely elosztott, tranzakciós, biztonságos és hordozható Java EE-és Jakarta EE-alkalmazások fejlesztésére szolgál. A EJB a vállalati bab nevű kiszolgálóoldali összetevőket használja egy alkalmazás üzleti logikájának megvalósításához, amely az újrafelhasználást is támogatja. További információért látogasson el [a EJB-alkalmazások fejlesztése az eap 7,2-on](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) , vagy [EJB-alkalmazások fejlesztése az EAP 7,3-on](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* Hibernált alkalmazások – a fejlesztők és a rendszergazdák a Java perzisztencia API-t (/hibernate-t) a JBoss EAP használatával fejleszthet és helyezhetik üzembe. A hibernálási mag egy objektum-összehasonlító leképezési keretrendszer a Java nyelvhez. Keretet biztosít egy objektumorientált tartományi modell egy kapcsolati adatbázishoz való leképezéséhez, amely lehetővé teszi az alkalmazások számára, hogy elkerülje a közvetlen interakciót az adatbázissal. A hibernálási entitások kezelője a [közös parlamenti közgyűlés 2,1 specifikációja](https://www.jcp.org/en/jsr/overview)által meghatározott programozási felületek és életciklus-szabályok megvalósítására szolgál. A hibernálási megjegyzésekkel együtt a burkoló egy teljes (és önálló) közös parlamenti megoldást valósít meg a kiforrott hibernálási mag felett. Ha többet szeretne megtudni a hibernált állapotáról, látogasson el az EAP [7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) vagy a  [jakartai megőrzés az EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api)-ben című témakörre.

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Red Hat Migration Toolkit for Applications (MTA)
A [Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) a Java-alkalmazáskiszolgáló áttelepítési eszköze. Ezzel az eszközzel áttelepítheti egy másik alkalmazás-kiszolgálóról a JBoss EAP-re. A szolgáltatás az [Eclipse ide](https://www.eclipse.org/ide/), a [Red Hat CodeReady-munkaterületek](https://developers.redhat.com/products/codeready-workspaces/overview)és a [Visual Studio Code (vs Code)](https://code.visualstudio.com/docs/languages/java) Javához készült ide-bővítményekkel működik.  Az MTA egy ingyenes és nyílt forráskódú eszköz, amely a következőket teszi:
* Alkalmazások elemzésének automatizálása
* Támogatási tevékenység becslése
* Gyorsítsa fel a kód áttelepítését
* Támogatási tárolókra bontás
* Integrálható az Azure munkaterhelés-szerkesztővel

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>A JBoss EAP migrálása a helyszínről az Azure-ba
A JBoss EAP on RHEL Azure Marketplace-ajánlata kevesebb, mint 20 perc alatt telepíthető és kiépíthető az Azure-beli virtuális gépeken. Ezeket az ajánlatokat az [Azure Marketplace](https://azuremarketplace.microsoft.com/) -en érheti el

Ez a Piactéri ajánlat az EAP és a RHEL különböző verzióinak különféle kombinációit tartalmazza a követelmények támogatásához. A JBoss EAP mindig saját előfizetéssel (BYOS) rendelkezik, de a RHEL operációs rendszer esetén választhat a BYOS vagy az utólagos elszámolású (TB) lehetőség közül. Az Azure Marketplace ajánlata önálló vagy fürtözött virtuális gépekként a RHEL-hez készült JBoss EAP-csomagra vonatkozó tervezési lehetőségeket is tartalmaz:

* JBoss EAP 7,2 a RHEL 7,7 virtuális gépen (TB)
* JBoss EAP 7,2 a RHEL 8,0 virtuális gépen (TB)
* JBoss EAP 7,3 a RHEL 8,0 virtuális gépen (TB)
* JBoss EAP 7,2 a RHEL 7,7 virtuális gépen (BYOS)
* JBoss EAP 7,2 a RHEL 8,0 virtuális gépen (BYOS)
* JBoss EAP 7,3 a RHEL 8,0 virtuális gépen (BYOS)

Az Azure Marketplace-ajánlatokkal együtt elérhetővé válik a gyors üzembe helyezési sablonok az Azure-beli áttelepítés megkezdéséhez. Ezek a rövid útmutatók olyan előre összeállított Azure Resource Manager (ARM) sablonokat és parancsfájlokat tartalmaznak, amelyekkel a JBoss EAP-t telepítheti a RHEL különböző konfigurációk és verziók kombinációjában. A következőkre lesz szüksége:

* Load Balancer (LB)
* Magánhálózati IP-cím terheléselosztáshoz és virtuális gépekhez
* Virtual Network (VNET) egyetlen alhálózattal
* Virtuális gép konfigurációja (fürt vagy önálló)
* Egy minta Java-alkalmazás

A sablonok megoldási architektúrája a következőket tartalmazza:

* JBoss EAP önálló RHEL virtuális gépen
* JBoss EAP fürtözött több RHEL virtuális gépen
* JBoss EAP fürtözött Azure virtuálisgép-méretezési csoport használatával

#### <a name="linux-workload-migration-for-jboss-eap"></a>Linux munkaterhelés-áttelepítés a JBoss EAP-hez
Az Azure munkaterhelés-szerkesztő leegyszerűsíti az Azure-ba irányuló helyszíni Java-alkalmazások megvalósíthatósági, kiértékelési és áttelepítési folyamatát. A munkaterhelés-szerkesztő integrálható Azure Migrate felderítési eszközzel a JBoss EAP-kiszolgálók azonosításához. Ezután dinamikusan létrehozza a JBoss EAP-kiszolgáló üzembe helyezésének Ansible-forgatókönyvét. Kihasználja a Red Hat MTA eszközt a kiszolgálók más alkalmazás-kiszolgálókról a JBoss EAP-re való átmigrálása céljából. A Migrálás egyszerűsítésének lépései a következők:
* Kiértékelés – JBoss EAP-fürtök az Azure virtuális gép vagy virtuálisgép-méretezési csoport használatával
* Értékelés – alkalmazásokat és infrastruktúrát vizsgál
* Infrastruktúra-konfiguráció – munkaterhelés-profilt hoz létre
* Üzembe helyezés és tesztelés – munkaterhelés üzembe helyezése, áttelepítése és tesztelése
* Üzembe helyezés utáni konfiguráció – az adatkezeléssel, a monitorozással, a biztonsággal, a biztonsági mentéssel és egyebekkel integrálható

## <a name="server-configuration-choice"></a>Kiszolgáló konfigurációjának választása

A RHEL virtuális gép üzembe helyezéséhez választhat a TB vagy a BYOS között. Az [Azure Marketplace](https://azuremarketplace.microsoft.com) -ről származó rendszerképek alapértelmezése a TB. Helyezzen üzembe egy BYOS-típust RHEL virtuális gépen, ha rendelkezik saját RHEL operációsrendszer-lemezképpel. A virtuális gépek vagy virtuálisgép-méretezési csoport üzembe helyezése előtt győződjön meg arról, hogy a RHSM-fiókja BYOS-jogosultsággal rendelkezik az F Cloud Access használatával.

A JBoss EAP hatékony felügyeleti képességekkel rendelkezik, valamint funkciókat és API-kat biztosít alkalmazásai számára. Ezek a felügyeleti funkciók attól függően eltérőek, hogy melyik üzemmódot kell használni a JBoss EAP elindításához. Ez a RHEL és a Windows Server rendszeren támogatott. A JBoss EAP önálló kiszolgálói működési módot biztosít a diszkrét példányok kezeléséhez. Emellett felügyelt tartományi működési módot is kínál a példányok csoportjai egyetlen felügyeleti pontról való kezelésére. Megjegyzés: a JBoss EAP által felügyelt tartományok nem támogatottak Microsoft Azureban, mivel a magas rendelkezésre állású (HA) funkciót az Azure infrastruktúra-szolgáltatások felügyelik. Az *EAP_HOME* nevű környezeti változó a JBoss EAP-telepítés elérési útjának jelölésére szolgál.

A **JBoss EAP elindítása önálló kiszolgálóként** – a következő parancs az EAP szolgáltatás önálló módban való elindításának módja.

```
$EAP_HOME/bin/standalone.sh
```
    
Ez az indítási parancsfájl a EAP_HOME/bin/standalone.conf fájlt használja az alapértelmezett beállítások (például a JVM beállítások) beállításához. A beállítások testreszabhatók ebben a fájlban. A JBoss EAP alapértelmezés szerint a standalone.xml konfigurációs fájl használatával indul el, de más módban is elindítható. Az elérhető önálló konfigurációs fájlokról és azok használatáról az EAP 7,2-hez készült [önálló kiszolgálói konfigurációs](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) fájlok, illetve az [EAP 7,3 önálló kiszolgálói konfigurációs fájljai](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) című részben olvashat bővebben. Ha a JBoss EAP-t egy másik konfigurációval szeretné elindítani, használja a--Server-config argumentumot. Például:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Az összes rendelkezésre álló indítási parancsfájl összes argumentumának és azok céljának teljes listáját a--help argumentummal vagy az EAP [7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) vagy a [Server Runtime argumentumai](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) alapján tekintheti meg az EAP 7,3 szakaszban.
    
A JBoss EAP fürtözött módban is működhet. További információkért tekintse meg az EAP [7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) vagy a fürtök áttekintését az EAP [ 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) -ben című témakörben. A JBoss EAP-fürt üzenetkezelése lehetővé teszi a JBoss EAP üzenetküldési kiszolgálók csoportosítását az üzenet-feldolgozási terhelés megosztásához. A fürt minden aktív csomópontja egy aktív JBoss EAP üzenetkezelő kiszolgáló, amely a saját üzeneteit kezeli, és a saját kapcsolatait kezeli.

## <a name="support-and-subscription-notes"></a>Támogatási és előfizetési megjegyzések
Ezek a rövid útmutató sablonok a következők: 

- A RHEL operációs rendszer TB vagy BYOS, Red Hat Gold képmodellen keresztül érhető el
- A JBoss EAP csak BYOS érhető el

#### <a name="using-rhel-os-with-payg-model"></a>A RHEL operációs rendszer használata a TB modellel

Alapértelmezés szerint ezek a rövid útmutatók az igény szerinti RHEL 7,7-es vagy 8,0-es TB-rendszerképet használják az Azure Gallery-ből. A TB-lemezképek további óránkénti RHEL előfizetési díjat kapnak a normál számítási, hálózati és tárolási költségek alapján. Ugyanakkor a rendszer regisztrálja a példányt a Red Hat-előfizetésében. Ez azt jelenti, hogy az egyik jogosultságát fogja használni. Ez a TB-rendszerkép a "dupla számlázás" kifejezést fogja eredményezni. Ezt a problémát elkerülheti saját RHEL-rendszerképének létrehozásával. További információért olvassa el ezt a Red Hat Tudásbázis (KB) című cikket, amely [bemutatja, hogyan kell RHEL virtuális gépet kiépíteni a Microsoft Azurehoz](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Vagy aktiválja a [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-rendszerképet.

Tekintse meg [Red Hat Enterprise Linux díjszabását](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) a TB virtuális gépek díjszabásával kapcsolatos részletekért. A RHEL TB-modellben való használatához szüksége lesz egy Azure-előfizetésre a [RHEL 7,7 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) vagy a [RHEL 8,0 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)megadott fizetési móddal. Ezen ajánlatok esetében az Azure-előfizetésben meg kell adni egy fizetési módot.

#### <a name="using-rhel-os-with-byos-model"></a>A RHEL operációs rendszer használata a BYOS modellel

Ahhoz, hogy a RHEL operációs rendszer BYOS használhassa, érvényes Red Hat-előfizetéssel kell rendelkeznie a RHEL operációs rendszer Azure-ban való használatához. A rövid útmutató telepítése előtt végezze el a következő előfeltételeket:

1. Győződjön meg arról, hogy rendelkezik a Red Hat-előfizetéséhez csatolt RHEL operációs rendszer-és JBoss EAP-jogosultságokkal.
2. Engedélyezze az Azure-előfizetés AZONOSÍTÓját a RHEL BYOS-lemezképek használatához. A folyamat befejezéséhez kövesse a [Red Hat előfizetés-kezelési (RHSM) dokumentációját](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) , amely a következő lépéseket tartalmazza:

    2,1 engedélyezze Microsoft Azure szolgáltatóként a Red Hat Cloud Access irányítópultján.

    2,2 adja hozzá az Azure-előfizetési azonosítóit.

    2,3 új termékek Felhőbeli hozzáférésének engedélyezése Microsoft Azureon.
    
    2,4 Az Azure-előfizetéshez tartozó Red Hat Gold-lemezképek aktiválása. További információért olvassa el az [előfizetések engedélyezése és fenntartása a Felhőbeli hozzáféréshez](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure) című fejezetet a további részletekért.

    2,5 várjon, amíg a Red Hat Gold images elérhetővé válik az Azure-előfizetésében. Ezek az arany lemezképek általában 3 órán belül elérhetők.
    
3. Fogadja el az Azure Marketplace használati feltételeit a RHEL BYOS-lemezképek esetében. Ezt a folyamatot az Azure Command-Line Interface (CLI) parancsainak futtatásával végezheti el az alábbiak szerint. További információkért tekintse meg a [RHEL BYOS Gold images in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos) dokumentációját a további részletekért. Fontos, hogy az Azure CLI legújabb verzióját futtatja.

    3,1 indítson el egy Azure CLI-munkamenetet, és végezzen hitelesítést az Azure-fiókjával. További segítségért tekintse meg az [Azure CLI-vel való bejelentkezést](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) ismertető témakört. Mielőtt továbblép, győződjön meg arról, hogy az Azure CLI legújabb verzióját futtatja.

    3,2 az alábbi CLI-parancs futtatásával ellenőrizze, hogy a RHEL BYOS-lemezképek elérhetők-e az előfizetésben. Ha itt nem talál eredményeket, tekintse meg a #2, és ellenőrizze, hogy az Azure-előfizetése aktiválva van-e a RHEL BYOS-lemezképek esetében.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3,3 futtassa a következő parancsot a Piactéri feltételek elfogadásához a RHEL 7,7 BYOS és a RHEL 8,0 BYOS.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. Az előfizetése most már készen áll a RHEL 7,7 vagy a 8,0 BYOS üzembe helyezésére az Azure Virtual Machines szolgáltatásban.

#### <a name="using-jboss-eap-with-byos-model"></a>A JBoss EAP használata a BYOS-modellel

A JBoss EAP csak a BYOS-modellen keresztül érhető el az Azure-on. A sablon telepítésekor meg kell adnia a RHSM hitelesítő adatait, valamint a RHSM-készlet AZONOSÍTÓját érvényes EAP-jogosultságokkal. Ha nem rendelkezik EAP-jogosultsággal, a Kezdés előtt szerezzen be egy [JBoss EAP próbaverziós előfizetést](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) .

## <a name="how-to-consume"></a>Használat

A sablont a következő három módon telepítheti:

- A PowerShell használata – a sablon üzembe helyezéséhez futtassa a következő parancsokat: (a Azure PowerShell telepítésével és konfigurálásával kapcsolatos információkért tekintse meg [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) .)

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Az Azure CLI használata – a sablon üzembe helyezéséhez futtassa a következő parancsokat: (tekintse meg az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -t az Azure CLI telepítésének és konfigurálásának részletes ismertetéséhez).

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Azure Portal használata – az alábbi szakaszban leírtak szerint telepítheti a Azure Portal az Azure rövid útmutató *sablonjaival* . A gyors útmutatóban kattintson a **telepítés az Azure** -ba vagy a **Tallózás gombra a githubon** .

## <a name="azure-quickstart-templates"></a>Azure Gyorsindítás sablonok

A RHEL-kombinációhoz tartozó JBoss EAP egyik rövid útmutató-sablonjaival kezdheti meg a telepítést, amely megfelel a telepítési célnak. Az alábbi lista az elérhető rövid útmutatók listáját tartalmazza.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP on RHEL (különálló virtuális gép)</a> – ez egy JBoss-EAP nevű webalkalmazás üzembe helyezése az Azure-ban a jboss EAP 7,2 vagy a 7,3 RHEL 7,7 vagy 8,0 virtuális gépen fut.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP on RHEL (fürtözött, több virtuális</a> gépre kiterjedő) – Ez egy EAP-Session-Replication nevű webalkalmazást helyez üzembe a jboss EAP 7,2 vagy 7,3 fürtön, amely n számú RHEL 7,7 vagy 8,0 virtuális gépen fut. Az "n" értéket a felhasználó határozza meg. A rendszer az összes virtuális gépet hozzáadja egy Load Balancer háttér-készletéhez.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP on RHEL (fürtözött, virtuálisgép-méretezési csoport)</a> – ez egy EAP-Session-Replication nevű webalkalmazást helyez üzembe a jboss EAP 7,2 vagy 7,3 fürtön, amely RHEL 7,7 vagy 8,0 virtuálisgép-méretezési csoport példányain fut.

## <a name="resource-links"></a>Erőforrás-hivatkozások:

* [Azure Hybrid-előnyök](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Java-alkalmazás konfigurálása Azure App Servicehoz](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP az Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP Azure app Service Linux rendszeren](https://docs.microsoft.com/azure/app-service/quickstart-java) QuickStart
* [A JBoss EAP üzembe helyezése Azure app Service](https://github.com/JasonFreeberg/jboss-on-app-service) oktatóanyagban

## <a name="next-steps"></a>Következő lépések

* További információ a [JBoss EAP 7,2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/) -ról
* További információ a [JBoss EAP 7,3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/) -ról
* További információ a [Red Hat előfizetés-kezelésről](https://access.redhat.com/products/red-hat-subscription-management)
* Microsoft docs for [Red Hat az Azure](https://aka.ms/rhel-docs) -ban
* [A JBoss EAP üzembe helyezése a RHEL VM/virtuálisgép-méretezési csoporton az Azure Marketplace-](https://aka.ms/AMP-JBoss-EAP) en
* [A JBoss EAP üzembe helyezése a RHEL VM/virtuálisgép-méretezési csoporton az Azure](https://aka.ms/Quickstart-JBoss-EAP) rövid útmutatóból
