---
title: "PowerShell - Azure tartományhoz HDInsight-fürtök konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítheti és konfigurálhatja az Azure PowerShell tartományhoz a HDInsight-fürtök"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 9da76bb5f649817cd2f027f3d0eb46d58a996b4f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>Az Azure PowerShell tartományhoz a HDInsight-fürtök (előzetes verzió) konfigurálása
Ismerje meg, hogyan állíthat be az Azure Active Directoryval (Azure AD) Azure HDInsight-fürtök és [Apache Pletyka](http://hortonworks.com/apache/ranger/) Azure PowerShell használatával. Az Azure PowerShell-parancsfájl valósul meg, hogy a konfiguráció gyorsabb és a kevesebb hiba nagyon eséllyel fordulnak elő. A HDInsight-tartományhoz csak Linux-alapú fürtökön konfigurálható. További információkért lásd: [bevezetni a tartományhoz a HDInsight-fürtök](hdinsight-domain-joined-introduction.md).

> [!IMPORTANT]
> Oozie nincs engedélyezve a HDInsight-tartományhoz.

A tipikus tartományhoz HDInsight fürt konfigurációjába a következő lépésekből áll:

1. Hozzon létre egy Azure klasszikus virtuális hálózatot az Azure AD.  
2. Hozzon létre, és konfigurálja az Azure AD és az Azure Active Directory tartományi Szolgáltatásokban.
3. A virtuális gépek hozzáadása a klasszikus virtuális hálózaton, a szervezeti egység létrehozásához. 
4. Az Azure Active Directory tartományi Szolgáltatásokban szervezeti egység létrehozása.
5. Hozzon létre egy HDInsight virtuális hálózatot az Azure-erőforrás felügyeleti üzemmódban.
6. DNS-névkeresési zónák beállítása az Azure Active Directory tartományi Szolgáltatásokban.
7. A két partner Vnetek.
8. HDInsight-fürtök létrehozása.

A PowerShell-parancsfájl a megadott 3-7 lépéseket végzi el. Haladjon végig 1 és 2. lépés manuálisan.  Ha nem szeretné használni az Azure PowerShell, lásd: [konfigurálása tartományhoz csatlakoztatott HDInsight-fürtök](hdinsight-domain-joined-configure.md). 

A végső topológia például a következőképpen néz ki:

![A HDInsight-topológia a tartományhoz](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

Mivel az Azure AD jelenleg csak a klasszikus virtuális hálózatokról (Vnetekről) támogatja, és Linux-alapú HDInsight-fürtök csak támogatási Azure Resource Manager Vnetek, HDInsight az Azure AD-integrációs van szükség, két virtuális hálózatokat és a társviszony-létesítés közöttük. A két üzembe helyezési modellek közötti összehasonlítás információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési: üzembe helyezési modellek és az erőforrások állapotát](../azure-resource-manager/resource-manager-deployment-model.md). A két Vnetek és az Azure Active Directory tartományi szolgáltatások ugyanabban a régióban kell lennie.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy nem rendelkezik az Azure AD. Ha nincs fiókja, ugorjon a 2. lépésben az a része.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag a következő elemeket kell tartalmaznia:

* Ismerje meg a [Azure AD tartományi szolgáltatások](https://azure.microsoft.com/services/active-directory-ds/) a [árképzési](https://azure.microsoft.com/pricing/details/active-directory-ds/) struktúra.
* Győződjön meg arról, hogy az előfizetés szerepel az engedélyezési listán az nyilvános előzetes verzió. Ehhez úgy, hogy küld egy e-mailek hdipreview@microsoft.com az előfizetés-azonosítóval.
* A tartomány egy aláíró hitelesítésszolgáltatóval által aláírt SSL-tanúsítvány. A tanúsítványra szükség van a biztonságos LDAP konfigurálásával. Önaláírt tanúsítványok nem használhatók.
* Azure PowerShell.  Lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>Hozzon létre egy Azure klasszikus virtuális hálózatot az Azure AD.
Az utasításokért lásd: [Itt](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic).

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>Hozzon létre, és konfigurálja az Azure AD és az Azure Active Directory tartományi Szolgáltatásokban.
Az utasításokért lásd: [Itt](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad).

## <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása
A PowerShell parancsfájl letölthető [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight). Bontsa ki a zip-fájl, és mentse helyileg a fájlt.

**A PowerShell parancsfájl szerkesztése**

1. Nyissa meg a Windows PowerShell ISE vagy bármilyen szövegszerkesztővel run.ps1.
2. Töltse ki a következő változók értékeit:
   
   * **$SubscriptionName** – az Azure-előfizetés hol szeretne létrehozni a HDInsight-fürt nevét. Már létrehozott egy klasszikus virtuális hálózatot az ehhez az előfizetéshez, és egy Azure Resource Manager virtuális hálózatot a HDInsight-fürthöz előfizetésen belül létrehozni.
   * **$ClassicVNetName** -a klasszikus virtuális hálózatot, amely tartalmazza az Azure Active Directory tartományi Szolgáltatásokban. Ez a virtuális hálózat felett biztosított ugyanahhoz az előfizetéshez kell lennie. Ez a virtuális hálózat az Azure portál használatával, és nem használja a klasszikus portálon kell létrehozni. Ha Ön leválasztásáról az [konfigurálása tartományhoz csatlakoztatott HDInsight-fürtök (előzetes verzió)](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic), az alapértelmezett név az contosoaadvnet.
   * **$ClassicResourceGroupName** – az erőforrás-kezelő csoport neve a klasszikus virtuális hálózat, amely a fent említett. Például contosoaadrg. 
   * **$ArmResourceGroupName** – az erőforráscsoport neve belül, amely, szeretne létrehozni a HDInsight-fürthöz. Ugyanabban az erőforráscsoportban $ArmResourceGroupName is használhatja.  Ha az erőforráscsoport nem létezik, a parancsfájl az erőforráscsoportot hoz létre.
   * **$ArmVNetName** – az erőforrás-kezelő virtuális hálózat neve, amelyen belül a HDInsight-fürthöz hozzárendelni kívánt. Ez a virtuális hálózat $ArmResourceGroupName bekerülnek.  Ha a virtuális hálózat nem létezik, a PowerShell parancsfájl létrehozza azt. Ha létezik, a fenti biztosító erőforráscsoportban kell.
   * **$AddressVnetAddressSpace** – a hálózati címterüket a Resource Manager virtuális hálózat. Győződjön meg arról, hogy ezen a címterületen elérhető legyen. Ez a címtartomány a klasszikus virtuális hálózat címtere nem lehetnek átfedésben. Például: "10.1.0.0/16"
   * **$ArmVnetSubnetName** -belül, amelyen el szeretné helyezni a HDInsight-fürt virtuális gépek az erőforrás-kezelő virtuális hálózati alhálózat neve. Ha az alhálózat nem létezik, a PowerShell parancsfájl létrehozza azt. Ha létezik, akkor a virtuális hálózat felett biztosító részének kell lennie.
   * **$AddressSubnetAddressSpace** – a hálózati címtartomány, az erőforrás-kezelő virtuális hálózati alhálózat. A HDInsight-fürt virtuális gép IP-címek a alhálózati címtartományt a lesz. Például "10.1.0.0/24."
   * **$ActiveDirectoryDomainName** – a fürt számára, hogy szeretné-e csatlakozni a HDInsight az Azure AD tartományi nevét. Például: "contoso.onmicrosoft.com"
   * **$ClusterUsersGroups** – a biztonsági csoportokat az ad-a HDInsight-fürthöz szinkronizálni kívánt köznapi neve. A biztonsági csoporthoz tartozó felhasználók fog tudni bejelentkezni a fürt-irányítópult a active directory tartományi hitelesítő adataik használatával. A biztonsági csoportokkal léteznie kell az active Directoryban. Például a "hiveusers" vagy "clusteroperatorusers" lehetőséget.
   * **$OrganizationalUnitName** -a szervezeti egységet a tartományban, amelyben el szeretné helyezni a HDInsight fürt a virtuális gépek és a fürt által használt szolgáltatásnevekről. A PowerShell-parancsfájlt hoz létre a szervezeti egység, ha nem létezik. Például "HDInsightOU."
3. Mentse a módosításokat.

**A parancsfájl futtatása**

1. Futtatás **Windows PowerShell** rendszergazdaként.
2. Keresse meg a mappát a run.ps1. 
3. A parancsfájl futtatásához írja be a fájl nevét, majd nyomja le **ENTER**.  3 bejelentkezési párbeszédpanelek előugró:
   
   1. **Jelentkezzen be a klasszikus Azure portálon** – adja meg a hitelesítő adatait, amely bejelentkezhet a klasszikus Azure portálon. Az Azure AD és az Azure Active Directory tartományi Szolgáltatásokban, ezek a hitelesítő adatok használatával kell hozott létre.
   2. **Jelentkezzen be Azure Resource Manager portálra** – Azure Resource Manager-portál aláírásához használt hitelesítő adatait.
   3. **Tartományi felhasználónév** – adja meg a hitelesítő adatait a tartományi felhasználó nevét, amely a HDInsight-fürt rendszergazdája szeretné. Ha létrehozott egy Azure AD teljesen új, kell hozott létre a felhasználó a dokumentum. 
      
      > [!IMPORTANT]
      > Adja meg a felhasználónevet a következő formátumban: 
      > 
      > Tartománynév\felhasználónév (például contoso.onmicrosoft.com\clusteradmin)
      > 
      > 
      
      Ez a felhasználó 3 jogosultsággal kell rendelkeznie: gépeket csatlakoztatni a megadott Active Directory-tartomány; szolgáltatásnevekről és a megadott szervezeti egység; gép objektumok létrehozása és a DNS-proxy fordított szabályok hozzáadását.

Névkeresési DNS-zónák létrehozásakor, a parancsfájl kérni fogja, hogy adjon meg egy hálózati. A hálózati Azonosítót a Resource Manager virtuális hálózat címelőtagjához kell lennie. Ha az erőforrás-kezelő virtuális hálózati alhálózat címteret 10.2.0.0/24, írja be például 10.2.0.0/24 amikor az eszköz bekéri a hálózati azonosítót. 

## <a name="create-hdinsight-cluster"></a>HDInsight-fürt létrehozása
Ebben a szakaszban egy Linux-alapú Hadoop fürt létrehozása hdinsight segítségével az Azure-portálon vagy [Azure Resource Manager sablon](../azure-resource-manager/resource-group-template-deploy.md). Egyéb Fürtlétrehozási módszerekhez és a beállítások ismertetése, lásd: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). Hdinsight Hadoop-fürtök létrehozása a Resource Manager-sablonnal kapcsolatos további információkért lásd: [létrehozása Hadoop-fürtök a HDInsight a Resource Manager-sablonok](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Az Azure portál használatával tartományhoz HDInsight fürt létrehozása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **új**, **Eszközintelligencia + analitika**, majd **HDInsight**.
3. Az a **új HDInsight-fürt** panelen adja meg vagy válassza ki a következő értékeket:
   
   * **Fürt neve**: Adja meg a tartományhoz csatlakoztatott HDInsight-fürt új fürt nevét.
   * **Előfizetés**: válassza ki a fürt létrehozásához használt Azure-előfizetéssel.
   * **Fürtkonfiguráció**:
     
     * **Fürt típusa**: Hadoop. Tartományhoz csatlakozó HDInsight jelenleg csak a támogatott Hadoop-fürtök.
     * **Operációs rendszer**: Linux.  HDInsight-tartományhoz csak a Linux-alapú HDInsight-fürtökön támogatott.
     * **Verzió**: Hadoop 2.7.3 (HDI 3.5). A HDInsight-tartományhoz csak HDInsight fürt 3.5-ös verziója támogatott.
     * **Fürt típusa**: prémium szintű
       
       Kattintson a **válasszon** menti a módosításokat.
   * **Hitelesítő adatok**: Adja meg a fürt mind az SSH-felhasználót a hitelesítő adatokat.
   * **Az adatforrás**: hozzon létre egy új tárfiókot, vagy az alapértelmezett tárfiók meglévő tárfiók használata a HDInsight-fürthöz. A hely ugyanaz, mint a két Vnetek kell lennie.  A helye is az a HDInsight-fürt.
   * **Árképzési**: válassza ki a fürt feldolgozó csomópontok számát.
   * **Speciális beállításokat**: 
     
     * **Tartományhoz való csatlakozás & virtuális hálózatot/alhálózatot**: 
       
       * **Megtörtént a tartománybeállítások**: 
         
         * **Tartománynév**: contoso.onmicrosoft.com
         * **Tartományi felhasználónév**: Adjon meg egy tartományi felhasználó nevét. Ez a tartomány a következő jogosultságokkal kell rendelkeznie: gépek csatlakoztatása a tartományhoz, és helyezze el őket a korábban megadott értékektől; szervezeti egység Szolgáltatásnevekről belül a korábban megadott értékektől; szervezeti egység létrehozása Névkeresési DNS-bejegyzéseket létrehozni. A tartományi felhasználó a rendszergazda a tartományhoz csatlakoztatott HDInsight-fürt lesz.
         * **Tartományi jelszó**: Adja meg a tartományi jelszó.
         * **Szervezeti egység**: Adja meg a korábban megadott értékektől szervezeti egység megkülönböztető nevét. Például: OU HDInsightOU, DC = contoso, DC = = onmicrosoft, DC = com
         * **LDAPS URL-cím**: ldaps://contoso.onmicrosoft.com:636
         * **Hozzáférés felhasználói csoport**: Adja meg a biztonsági csoportja, amelynek felhasználói kívánja sync és a fürt számára. Például HiveUsers.
           
           Kattintson a **válasszon** menti a módosításokat.
           
           ![Tartományhoz csatlakozó HDInsight portal tartomány beállítás konfigurálása](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Virtuális hálózati**: contosohdivnet
       * **Alhálózati**: Alhalozat_1
         
         Kattintson a **válasszon** menti a módosításokat.        
         Kattintson a **válasszon** menti a módosításokat.
   * **Erőforráscsoport**: válassza ki az erőforráscsoportot, a HDInsight vnet (contosohdirg) használt.
4. Kattintson a **Create** (Létrehozás) gombra.  

Egy másik lehetőség a tartományhoz csatlakoztatott HDInsight-fürt létrehozása az Azure Resource Manager-sablonnal használ. Az alábbi eljárás bemutatja, hogyan:

**Egy erőforrás-kezelés sablon használatával tartományhoz HDInsight-fürt létrehozása**

1. Kattintson az alábbi képre kattintva nyissa meg a Resource Manager-sablon az Azure portálon. A Resource Manager-sablon a következő nyilvános blobtárolóban található. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Az a **paraméterek** panelen adja meg a következő értékeket:
   
   * **Előfizetés**: (válassza ki az Azure-előfizetéshez).
   * **Erőforráscsoport**: kattintson a **meglévő**, és adja meg a használt ugyanabban az erőforráscsoportban.  Például contosohdirg. 
   * **Hely**: Adjon meg egy erőforráscsoport helye.
   * **Fürt neve**: Adjon nevet a létrehozandó Hadoop-fürtnek. Például contosohdicluster.
   * **Fürt típusa**: válassza ki a fürt típusa.  Az alapértelmezett érték **hadoop**.
   * **Hely**: jelöljön ki egy helyet a fürthöz.  Az alapértelmezett tárfiókot használja ugyanazt a helyet.
   * **A fürt feldolgozó csomópontok száma**: válassza ki a feldolgozó csomópontok száma.
   * **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az **admin**.
   * **SSH-felhasználónév és jelszó**: Az alapértelmezett felhasználónév az **sshuser**.  Ezt át lehet nevezni. 
   * **Virtuális hálózati azonosító**: /subscriptions/&lt;; előfizetés-azonosító > /resourceGroups/&lt;erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >
   * **Virtuális hálózati alhálózat**: /subscriptions/&lt;; előfizetés-azonosító > /resourceGroups/&lt;erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >/alhálózatok/Alhalozat_1
   * **Tartománynév**: contoso.onmicrosoft.com
   * **Szervezeti egység megkülönböztető név**: OU HDInsightOU, DC = contoso, DC = = onmicrosoft, DC = com
   * **Fürt felhasználók csoport D Ns**: "\"CN HiveUsers, OU = AADDC Users, DC = =<DomainName>, DC = onmicrosoft, DC = com\""
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (írja be a tartományi rendszergazda felhasználóneve)
   * **Értéket**: (írja be a tartomány rendszergazdai jelszóval)
   * **Elfogadom a feltételeket és a fenti feltételek**: (ellenőrzés)
   * **Rögzítés az irányítópulton**: (ellenőrzés)
3. Kattintson a **Purchase** (Vásárlás) gombra. Egy új csempe jelenik meg **Deploying Template deployment** (Üzembe helyezés – Sablon telepítése) címmel. Egy fürt létrehozása nagyjából 20 percet vesz igénybe. A fürt létrehozása után kattintson a portálon, hogy megnyissa a fürt paneljén.

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. A fürtök törlésével utasításokért lásd: [kezelése Hadoop-fürtök a HDInsight az Azure portál használatával](hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Következő lépések

* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](hdinsight-domain-joined-run-hive.md).
* Az SSH használatával csatlakozni a tartományhoz a HDInsight-fürtök, lásd: [az SSH a Hdinsighttal](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

