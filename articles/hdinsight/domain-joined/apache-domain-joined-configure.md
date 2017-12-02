---
title: "A tartományhoz a HDInsight-fürtök - Azure konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítheti és konfigurálhatja a tartományhoz a HDInsight-fürtök"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 649d138a85ca47440e43c00637ee92b86f4eb03e
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="configure-domain-joined-hdinsight-clusters"></a>A tartományhoz a HDInsight-fürtök konfigurálása

Ismerje meg, hogyan állíthat be az Azure Active Directoryval (Azure AD) Azure HDInsight-fürtök és [Apache Pletyka](http://hortonworks.com/apache/ranger/) kihasználásához erős hitelesítést és a gazdag szerepköralapú hozzáférés-vezérlést (RBAC) házirendek.  A HDInsight-tartományhoz csak Linux-alapú fürtökön konfigurálható. További információkért lásd: [bevezetni a tartományhoz a HDInsight-fürtök](apache-domain-joined-introduction.md).

> [!IMPORTANT]
> Oozie nincs engedélyezve a HDInsight-tartományhoz.

Ez a cikk az sorozat első oktatóanyaga:

* Hozzon létre egy HDInsight-fürthöz az Azure AD (az Azure Directory tartományi szolgáltatások képesség) keresztül csatlakozó Apache Pletyka engedélyezve van.
* Hozzon létre és Pletyka Apache Hive-házirendeket alkalmazni, és engedélyezése a felhasználók (például adatszakértőkön) való kapcsolódáshoz a Hive ODBC-alapú eszközökkel, például az Excel, a Tableau stb. A Microsoft egyéb munkaterhelések, például a HBase és a Storm, hozzáadása HDInsight tartományhoz hamarosan dolgozik.

Azure-szolgáltatás nevének globálisan egyedinek kell lennie. A következő neveket ebben az oktatóanyagban használnak. Contoso nevű fiktív. Le kell cserélnie *contoso* valamilyen más névvel, ha az oktatóanyag lépéseinek követése. 

**Nevek:**

| Tulajdonság | Érték |
| --- | --- |
| Azure AD-címtár |contosoaaddirectory |
| Az Azure AD-tartomány neve |Contoso (contoso.onmicrosoft.com) |
| HDInsight virtuális hálózat |contosohdivnet |
| HDInsight VNet erőforráscsoport |contosohdirg |
| HDInsight-fürt |contosohdicluster |

Ez az oktatóanyag lépéseit egy tartományhoz csatlakozó HDInsight-fürt konfigurálásához. Minden szakasz további cikkeit mutató hivatkozásokat tartalmaz további információt.

## <a name="prerequisite"></a>Előfeltétel:
* Ismerje meg a [Azure AD tartományi szolgáltatások](https://azure.microsoft.com/services/active-directory-ds/) a [árképzési](https://azure.microsoft.com/pricing/details/active-directory-ds/) struktúra.
* Győződjön meg arról, hogy az előfizetés szerepel az engedélyezési listán az nyilvános előzetes verzió. Ehhez úgy, hogy küld egy e-mailek hdipreview@microsoft.com az előfizetés-azonosítóval.
* A tartomány egy aláíró hitelesítésszolgáltatóval, vagy önaláírt tanúsítványt által aláírt SSL-tanúsítvány. A tanúsítványra szükség a biztonságos LDAP beállítása.

## <a name="procedures"></a>Eljárások
1. Hozzon létre egy HDInsight virtuális hálózatot az Azure-erőforrás felügyeleti üzemmódban.
2. Hozzon létre, és konfigurálja az Azure AD és az Azure Active Directory tartományi Szolgáltatásokban.
3. HDInsight-fürtök létrehozása.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy nem rendelkezik az Azure AD. Ha nincs fiókja, kihagyhatja a része.
> 
> 

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>A HDInsight-fürt erőforrás-kezelő VNet létrehozása
Ez a szakasz hoz létre egy Azure Resource Manager virtuális hálózatot, amely a HDInsight-fürtöt fog történni. Azure-hálózatok más módszerekkel egyéni további információkért lásd: [virtuális hálózat létrehozása](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

Miután létrehozta a virtuális hálózat, konfigurál majd az Azure Active Directory tartományi szolgáltatások a virtuális hálózatot.

**Erőforrás-kezelő VNet létrehozása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **új**, **hálózati**, majd **virtuális hálózati**. 
3. A **telepítési modell kiválasztása**, jelölje be **erőforrás-kezelő**, és kattintson a **létrehozása**.
4. Írja be vagy válassza ki az alábbi értékeket:
   
   * **Név**: contosohdivnet
   * **Címtér**: 10.0.0.0/16.
   * **Alhálózati név**: Alhalozat_1
   * **Alhálózati címtartományt**: 10.0.0.0/24
   * **Előfizetés**: (válassza ki az Azure-előfizetéshez.)
   * **Erőforráscsoport**: contosohdirg
   * **Hely**: (jelöljön ki azonos helyen, az Azure AD virtuális hálózatot. Például contosoaadvnet.)
5. Kattintson a **Create** (Létrehozás) gombra.

**Az erőforrás-kezelő VNet DNS konfigurálása**

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **további szolgáltatások** > **virtuális hálózatok**. Győződjön meg arról, hogy ne kattintson **virtuális hálózatok (klasszikus)**.
2. Kattintson a **contosohdivnet**.
3. Kattintson a **DNS-kiszolgálók** az új panel bal oldalán.
4. Kattintson a **egyéni**, és írja be a következő értékeket:
   
   * 10.0.0.4
   * 10.0.0.5     
     
5. Kattintson a **Save** (Mentés) gombra.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Hozzon létre, és az Azure Active Directory tartományi szolgáltatások konfigurálása az Azure AD
Ez a szakasz tartalma:

1. Hozzon létre egy Azure AD.
2. Az Azure AD-felhasználók létrehozásához. Ezek a felhasználók tartományi felhasználók. Az első felhasználó használhatja az Azure AD-val a HDInsight-fürt konfigurálásához.  Ebben az oktatóanyagban a más felhasználók két nem kötelező. A használandó [konfigurálása Hive házirendek a tartományhoz a HDInsight-fürtök](apache-domain-joined-run-hive.md) Apache Pletyka házirendek konfigurálásakor.
3. Hozzon létre az AAD DC rendszergazdák csoportot, és az Azure AD-felhasználó hozzáadása a csoporthoz. Ez a felhasználó használhatja a szervezeti egység létrehozása.
4. Engedélyezi az Azure AD tartományi szolgáltatásokat (az Azure AD DS) az Azure AD.
5. LDAPS konfigurálása az Azure AD. Olvasási és írási az Azure AD a Lightweight Directory Access Protocol (LDAP) segítségével.

Ha szeretné használni egy meglévő Azure AD, kihagyhatja a 1. és 2.

**Az Azure AD létrehozása**

1. Az a [a klasszikus Azure portálon](https://manage.windowsazure.com), kattintson a **új** > **alkalmazásszolgáltatások** > **Active Directory**  >  **Directory** > **egyéni létrehozás**. 
2. Adja meg vagy válassza ki a következő értékeket:
   
   * **Név**: contosoaaddirectory
   * **Tartománynév**: contoso.  Ez a név globálisan egyedinek kell lennie.
   * **Ország vagy régió**: válassza ki az országában vagy régiójában.
3. Kattintson a **Befejezés** gombra.

**Hozzon létre egy Azure AD-felhasználó**

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **Azure Active Directory** > **contosoaaddirectory** > **felhasználók és csoportok**. 
2. Kattintson a **minden felhasználó** a menüből.
3. Kattintson a **új felhasználó**.
4. Adja meg **neve** és **felhasználónév**, és kattintson a **következő**. 
5. Felhasználói profil; konfigurálása A **szerepkör**, jelölje be **globális rendszergazda**; majd **következő**.  A globális rendszergazdai szerepkörrel szervezeti egységek létrehozásához szükséges.
6. Készítsen másolatot az ideiglenes jelszót.
7. Kattintson a **Create** (Létrehozás) gombra. Az oktatóanyag későbbi részében szüksége lesz az a globális rendszergazdai jogú felhasználó létrehozása a HDInsight-fürthöz.

Kövesse ugyanezt az eljárást a két további felhasználók létrehozása a **felhasználói** szerepkör, hiveuser1 és hiveuser2. A következő felhasználók használandó [tartományhoz a HDInsight-fürtök házirendek konfigurálása Hive](apache-domain-joined-run-hive.md).

**Az aad-ben DC rendszergazdák csoport létrehozása, és az Azure AD-felhasználó hozzáadása**

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **Azure Active Directory** > **contosoaaddirectory** > **felhasználók és csoportok**. 
2. Kattintson a **összes csoport** a felső menüben.
3. Kattintson a **új csoport**.
4. Adja meg vagy válassza ki a következő értékeket:
   
   * **Név**: DC rendszergazdák aad-ben.  A csoport neve nem módosítható.
   * **Tagsági típusa**: rendelve.
5. Kattintson a **Kiválasztás** gombra.
6. Kattintson a **tagok**.
7. Válassza ki az első felhasználót az előző lépésben létrehozott, és kattintson **válasszon**.
8. Ismételje meg a azonos nevű másik csoport létrehozása **HiveUsers**, és a két Hive felhasználók hozzáadása a csoporthoz.

További információkért lásd: [Azure AD tartományi szolgáltatások (előzetes verzió) – a "AAD DC rendszergazdák" csoport létrehozása](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Az Azure AD az Azure Active Directory tartományi szolgáltatások engedélyezése**

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **hozzon létre egy erőforrást** > **biztonság + identitás szakaszában** > **AzureADtartományiszolgáltatások**  >  **Hozzáadása**. 
2. Adja meg vagy válassza ki a következő értékeket:
   * **Könyvtárnév**: contosoaaddirectory
   * **DNS-tartománynév**: Ez azt jelenti, hogy az Azure-címtár alapértelmezett DNS-nevét. Például a contoso.onmicrosoft.com.
   * **Hely**: válassza ki a régiót.
   * **Hálózati**: válassza ki a virtuális hálózat és a korábban létrehozott alhálózati. Például **contosohdivnet**.
3. Kattintson a **OK** összefoglaló lapról. Látni fogja **telepítés folyamatban...**  értesítések alatt.
4. Várjon, amíg **telepítés folyamatban...**  eltűnik, és **IP-cím** lekérdezi feltöltve. Két IP-címet fogja lekérni feltöltve. Ezek azok a tartományvezérlők, tartományi szolgáltatások által kiosztott IP-címét. Minden IP-cím után létesítése sikeres, és készen áll a megfelelő tartományvezérlő lesznek láthatók. Jegyezze fel a két IP-címet. Később szüksége lesz rájuk.

További információkért lásd: [engedélyezése Azure Active Directory tartományi szolgáltatások az Azure portál használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Jelszó**

Ha a saját tartomány használata esetén a jelszó szeretné. Lásd: [engedélyezése a jelszó-szinkronizálás az Azure AD tartományi szolgáltatásokra kizárólag felhőalapú Azure AD-címtár](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

**LDAPS konfigurálása az Azure AD**

1. A tartomány egy aláíró hitelesítésszolgáltatóval által aláírt SSL-tanúsítvány beszerzése.
2. Az a [Azure-portálon](https://portal.azure.com), kattintson a **Azure AD tartományi szolgáltatások** > **contoso.onmicrosoft.com**. 
3. Engedélyezése **biztonságos LDAP**.
6. Kövesse az utasításokat a tanúsítványfájl és a jelszó megadását.  
7. Várjon, amíg **biztonságos LDAP tanúsítvány** fel lett töltve. A is tarthat 10 perc vagy több.

> [!NOTE]
> Az Azure Active Directory tartományi szolgáltatások néhány háttérfeladatok készül futtatni, jelenhet meg tanúsítványának feltöltése - hibát <i>van egy művelet végrehajtás alatt álló ennél a bérlőnél. Próbálkozzon újra később</i>.  Ha ezt a hibát tapasztal, próbálja meg újra némi várakozás után. A második tartományvezérlő IP-Címének úgy kell létrehozni, akár 3 órát is igénybe vehet.
> 
> 

További információkért lásd: [konfigurálása biztonságos LDAP (LDAPS) egy Azure AD tartományi szolgáltatások által felügyelt tartomány](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-hdinsight-cluster"></a>HDInsight-fürt létrehozása
Ebben a szakaszban egy Linux-alapú Hadoop fürt létrehozása hdinsight segítségével az Azure-portálon vagy [Azure Resource Manager sablon](../../azure-resource-manager/resource-group-template-deploy.md). Egyéb Fürtlétrehozási módszerekhez és a beállítások ismertetése, lásd: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md). Hdinsight Hadoop-fürtök létrehozása a Resource Manager-sablonnal kapcsolatos további információkért lásd: [létrehozása Hadoop-fürtök a HDInsight a Resource Manager-sablonok](../hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Az Azure portál használatával tartományhoz HDInsight fürt létrehozása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **új**, **Eszközintelligencia + analitika**, majd **HDInsight**.
3. Az a **új HDInsight-fürt** panelen adja meg vagy válassza ki a következő értékeket:
   
   * **Fürt neve**: Adja meg a tartományhoz csatlakoztatott HDInsight-fürt új fürt nevét.
   * **Előfizetés**: válassza ki a fürt létrehozásához használt Azure-előfizetéssel.
   * **Fürtkonfiguráció**:
     
     * **Fürt típusa**: Hadoop. Tartományhoz csatlakoztatott HDInsight jelenleg csak a támogatott Hadoop, Spark, és interaktív lekérdezés fürtök.
     * **Operációs rendszer**: Linux.  HDInsight-tartományhoz csak a Linux-alapú HDInsight-fürtökön támogatott.
     * **Verzió**: HDI 3.6. A HDInsight-tartományhoz csak a HDInsight-fürt verziószáma 3.6 támogatott.
     * **Fürt típusa**: prémium szintű
       
       Kattintson a **válasszon** menti a módosításokat.
   * **Hitelesítő adatok**: Adja meg a fürt mind az SSH-felhasználót a hitelesítő adatokat.
   * **Az adatforrás**: hozzon létre egy új tárfiókot, vagy az alapértelmezett tárfiók meglévő tárfiók használata a HDInsight-fürthöz. A hely ugyanaz, mint a két Vnetek kell lennie.  A helye is az a HDInsight-fürt.
   * **Árképzési**: válassza ki a fürt feldolgozó csomópontok számát.
   * **Speciális beállításokat**: 
     
     * **Tartományhoz való csatlakozás & virtuális hálózatot/alhálózatot**: 
       
       * **Megtörtént a tartománybeállítások**: 
         
         * **Tartománynév**: contoso.onmicrosoft.com
         * **Tartományi felhasználónév**: Adjon meg egy tartományi felhasználó nevét. Ez a tartomány a következő jogosultságokkal kell rendelkeznie: gépek csatlakoztatása a tartományhoz, és helyezze el őket a szervezeti egység során fürt létrehozása; Szolgáltatásnevekről belül a fürt létrehozása; során megadott szervezeti egység létrehozása Névkeresési DNS-bejegyzéseket létrehozni. A tartományi felhasználó a rendszergazda a tartományhoz csatlakoztatott HDInsight-fürt lesz.
         * **Tartományi jelszó**: Adja meg a tartományi jelszó.
         * **Szervezeti egység**: Adja meg a HDInsight-fürthöz használni kívánt szervezeti egység megkülönböztető nevét. Például: OU HDInsightOU, DC = contoso, DC = = onmicrosoft, DC = com. Ha a szervezeti egység nem létezik, a HDInsight-fürt megkísérli a szervezeti egység létrehozása. Győződjön meg arról, hogy a szervezeti Egységet már létezik, vagy a tartományi fiók jogosult az hozzon létre egy újat. Ha AADDC rendszergazdák részét képező a tartományi fiókot használni, azt kell jogosult arra, hogy a szervezeti egység létrehozása.
         * **LDAPS URL-cím**: ldaps://contoso.onmicrosoft.com:636
         * **Hozzáférés felhasználói csoport**: Adja meg a biztonsági csoport, amelynek felhasználók szeretné szinkronizálni a fürthöz. Például HiveUsers.
           
           Kattintson a **válasszon** menti a módosításokat.
           
           ![Tartományhoz csatlakozó HDInsight portal tartomány beállítás konfigurálása](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Virtuális hálózati**: contosohdivnet
       * **Alhálózati**: Alhalozat_1
         
         Kattintson a **válasszon** menti a módosításokat.        
         Kattintson a **válasszon** menti a módosításokat.
   * **Erőforráscsoport**: válassza ki az erőforráscsoportot, a HDInsight vnet (contosohdirg) használt.
4. Kattintson a **Create** (Létrehozás) gombra.  

Egy másik lehetőség a tartományhoz csatlakoztatott HDInsight-fürt létrehozása az Azure Resource Manager-sablonnal használ. Az alábbi eljárás bemutatja, hogyan:

**Egy erőforrás-kezelés sablon használatával tartományhoz HDInsight-fürt létrehozása**

1. Kattintson az alábbi képre kattintva nyissa meg a Resource Manager-sablon az Azure portálon. A Resource Manager-sablon a következő nyilvános blobtárolóban található. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
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
   * **Fürt felhasználói csoport DNs**: [\"HiveUsers\"]
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (írja be a tartományi rendszergazda felhasználóneve)
   * **Értéket**: (írja be a tartomány rendszergazdai jelszóval)
   * **Elfogadom a feltételeket és a fenti feltételek**: (ellenőrzés)
   * **Rögzítés az irányítópulton**: (ellenőrzés)
3. Kattintson a **Purchase** (Vásárlás) gombra. Egy új csempe jelenik meg **Deploying Template deployment** (Üzembe helyezés – Sablon telepítése) címmel. Egy fürt létrehozása nagyjából 20 percet vesz igénybe. A fürt létrehozása után kattintson a portálon, hogy megnyissa a fürt paneljén.

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. A fürtök törlésével utasításokért lásd: [kezelése Hadoop-fürtök a HDInsight az Azure portál használatával](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Következő lépések
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozni a tartományhoz a HDInsight-fürtök, lásd: [SSH használata a HDInsight Linux, Unix vagy OS X, Linux-alapú Hadooppal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

