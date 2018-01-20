---
title: "A tartományhoz a HDInsight-fürtök - Azure konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítheti és konfigurálhatja a tartományhoz a HDInsight-fürtök"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: cgronlun
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: saurinsh
ms.openlocfilehash: e24ea43f6df3c22c338646af21f7517e16b718ea
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>Tartományhoz csatlakozó HDInsight védőfal mögötti környezet konfigurálása

Ismerje meg, hogyan állíthat be az Active Directory önálló Azure HDInsight-fürtök és [Apache Pletyka](http://hortonworks.com/apache/ranger/) kihasználásához erős hitelesítést és a gazdag szerepköralapú hozzáférés-vezérlést (RBAC) házirendek. További információkért lásd: [bevezetni a tartományhoz a HDInsight-fürtök](apache-domain-joined-introduction.md).

Nélkül tartományhoz HDInsight-fürtöt, az egyes fürtökön csak van a Hadoop HTTP-felhasználói fiókot, és egy SSH-felhasználói fiókot.  A több felhasználó-hitelesítési elérhető használatával:

-   Az Active Directory önálló Azure infrastruktúra-szolgáltatáson futó.
-   Azure Active Directory.
-   Az ügyfél helyszíni környezetben futó Active Directory.

Active Directory önálló Ez a cikk az Azure infrastruktúra-szolgáltatáson futó vonatkozik. Az ügyfél követve többfelhasználós támogatás kérése a HDInsight a legegyszerűbb architektúra. Ez a cikk két megközelítés ehhez a konfigurációhoz terjed ki:

- 1. lehetőség: Az önálló active Directoryból és a HDInsight-fürt létrehozásához egy Azure-erőforrás management-sablonnal használ.
- 2. lehetőség: A teljes folyamat rendszer darabolja fel a következő lépéseket:
    - Hozzon létre egy Active Directory-sablon használatával.
    - A telepítő LDAPS.
    - Active Directory-felhasználók és csoportok létrehozása
    - HDInsight-fürt létrehozása

> [!IMPORTANT]
> Oozie nincs engedélyezve a HDInsight-tartományhoz.

## <a name="prerequisite"></a>Előfeltétel
* Azure-előfizetés

## <a name="option-1-one-step-approach"></a>1. lehetőség: egylépéses megközelítés
Ebben a szakaszban egy Azure-erőforrás management-sablon Azure-portálról megnyitásához. A sablon segítségével létrehozhat egy önálló Active Directory és a HDInsight-fürtöt. Jelenleg Hadoop-fürt tartományhoz, a Spark-fürt és a fürt interaktív lekérdezést is létrehozhat.

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon. A sablon az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) között található.
   
    Spark-fürt létrehozása:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fspark%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Az interaktív lekérdezés fürt létrehozásához:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Finteractivequery%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    A Hadoop fürtök létrehozásához:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fhadoop%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Adja meg az értékeket, válasszon **elfogadom a feltételeket és a fenti feltételek**, jelölje be **rögzítés az irányítópulton**, és kattintson a **beszerzési**. Vigye az egérmutatót a magyarázat bejelentkezhet a mezők melletti leírásában olvasható. A legtöbb értékek vannak-e töltve. Az alapértelmezett értékeket, vagy saját értékeket is használhatja.

    - **Erőforráscsoport**: Adjon meg egy Azure erőforráscsoport-név.
    - **Hely**: Válasszon egy helyet, amely közel található Önhöz.
    - **Új Tárfiók neve**: Adja meg egy Azure Storage-fiók nevét. Az új tárfiók lesz az elsődleges tartományvezérlő, a tartalék kiszolgáló és a HDInsight-fürthöz az alapértelmezett tárfiók.
    - **Rendszergazda felhasználóneve**: Adja meg a tartomány rendszergazdai jogosultságú felhasználónevet.
    - **Rendszergazdai jelszó**: Adja meg a tartományi rendszergazda jelszavával.
    - **Tartománynév**: az alapértelmezett név az *contoso.com*.  Ha módosítja a tartomány nevét, frissíteni kell a **biztonságos LDAP tanúsítvány** mező és a **szervezeti egységet DN** mező.
    - **A fürt neve**: Adja meg a HDInsight fürt nevét.
    - **Fürt típusa**: ne módosítsa ezt az értéket. Ha a fürt típusa módosítani kívánja, használja az adott sablon az előző lépésben.

    Néhány értéket változtatható a sablonban, például a munkavégző csomópont példányszám két.  A kódolt értékek módosításához kattintson **Szerkesztés sablon**.

    ![A HDInsight fürt tartományhoz Szerkesztés sablon](./media/apache-domain-joined-configure/hdinsight-domain-joined-edit-template.png)

A sablon sikeres befejezését követően nincsenek 23 erőforrások létrehozása az erőforráscsoportban.

## <a name="option-2-multi-step-approach"></a>2. lehetőség: többlépéses megközelítés

Ebben a szakaszban négy lépésben történik:

1. Hozzon létre egy Active Directory-sablon használatával.
2. A telepítő LDAPS.
3. Active Directory-felhasználók és csoportok létrehozása
4. HDInsight-fürt létrehozása

### <a name="create-an-active-directory"></a>Hozzon létre egy Active Directory

Az Azure Resource Manager-sablon megkönnyíti az Azure-erőforrások létrehozása. Ebben a szakaszban használhatja egy [Azure gyors üzembe helyezési sablon](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/) hozzon létre egy új erdő és a tartomány két virtuális gép számára. A két virtuális gép az elsődleges tartományvezérlő és a biztonsági mentési tartományvezérlő szolgál.

**Két tartományvezérlő a tartomány létrehozása**

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    A sablon néz ki:

    ![HDInsight tartományhoz az erdő tartományi virtuális gépek létrehozása](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. Írja be a következő értékeket:

    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Az erőforráscsoport neve**: írja be az erőforráscsoport nevét.  Egy erőforráscsoportot az Azure-projekt kapcsolódó erőforrások kezelésére szolgál.
    - **Hely**: válassza ki az Azure-beli hely, amely közel található Önhöz.
    - **Rendszergazda felhasználóneve**: Ez a tartomány rendszergazdai jogosultságú felhasználónevet. A felhasználó nincs a HTTP-felhasználói fiók a HDInsight-fürt. Ez az oktatóanyag során használt fiók.
    - **Rendszergazdai jelszó**: Adja meg a jelszót a tartományi rendszergazda.
    - **Tartománynév**: A tartománynév kétrészes nevét kell lennie. Például: contoso.com, vagy a contoso.local, illetve hdinsight.test.
    - **DNS-előtagja**: írja be a DNS-előtagja
    - **Az elsődleges tartományvezérlő RDP-portjára**: (az alapértelmezett értéket használja a jelen oktatóanyag)
    - **Tartalék RDP-portjára**: (az alapértelmezett értéket használja a jelen oktatóanyag)
    - **az összetevők hely**: (az alapértelmezett értéket használja a jelen oktatóanyag)
    - **az összetevők hely SAS-jogkivonat**: (hagyja üresen az oktatóanyag.)

Az erőforrások létrehozása nagyjából 20 percet vesz igénybe.

### <a name="setup-ldaps"></a>A telepítő LDAPS

Olvasási és írási ad a Lightweight Directory Access Protocol (LDAP) segítségével.

**A távoli asztali kapcsolattal PDC való kapcsolódáshoz**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg az erőforráscsoportot, és nyissa meg a elsődleges tartományvezérlő (PDC) virtuális gépet. Alapértelmezés szerint az elsődleges tartományvezérlő ez adPDC. 
3. Kattintson a **Connect** kapcsolódni a távoli asztali kapcsolattal az elsődleges.

    ![HDInsight tartományhoz csatlakozzon az elsődleges tartományvezérlő távoli asztal](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**Az Active Directory tanúsítványszolgáltatás hozzáadása**

4. Nyissa meg **Kiszolgálókezelő** Ha nincs megnyitva.
5. Kattintson a **kezelése**, és kattintson a **szerepkörök és szolgáltatások hozzáadása**.

    ![A HDInsight-tartományhoz csatlakoztatott szerepkörök és szolgáltatások hozzáadása](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. A "Megkezdése előtt", kattintson az **következő**.
6. Válassza ki **szerepköralapú vagy szolgáltatásalapú telepítés**, és kattintson a **következő**.
7. Válassza ki az elsődleges tartományvezérlő, és kattintson a **következő**.  Alapértelmezés szerint az elsődleges tartományvezérlő ez adPDC.
8. Válassza ki **Active Directory tanúsítványszolgáltatások**.
9. Kattintson a **szolgáltatások hozzáadása** az előugró párbeszédpanelen.
10. Kövesse a varázsló utasításait, az alapértelmezett beállításokat használja a többi az eljárást.
11. A varázsló bezárásához kattintson a **Bezárás** gombra.

**AD-tanúsítvány konfigurálása**

1. A Kiszolgálókezelőben kattintson a sárga értesítési ikon, majd **az Active Directory-alapú konfigurálása szolgáltatások**.

    ![A HDInsight-tartományhoz AD tanúsítvány konfigurálása](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. Kattintson a **szerepkör-szolgáltatások** válassza ki a bal oldali **hitelesítésszolgáltató**, és kattintson a **következő**.
3. Kövesse a varázsló utasításait, az alapértelmezett beállításokat használja az eljárást a többi (kattintson **konfigurálása** az utolsó lépésnél).
4. A varázsló bezárásához kattintson a **Bezárás** gombra.

### <a name="optional-create-ad-users-and-groups"></a>(Választható) Active Directory-felhasználók és csoportok létrehozása

**Az ad felhasználók és csoportok létrehozása**
1. Csatlakozzon az elsődleges tartományvezérlő távoli asztali kapcsolattal
1. Nyissa meg **Active Directory – felhasználók és számítógépek**.
2. A bal oldali panelen válassza ki a tartomány nevét.
3. Kattintson a **új felhasználó létrehozása az aktuális tárolóban** ikonra a felső menüben.

    ![HDInsight tartományhoz csatlakozó felhasználók létrehozása](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. Kövesse az utasításokat, néhány felhasználó létrehozásához. Például hiveuser1 és hiveuser2.
5. Kattintson a **hozzon létre egy új csoportot az aktuális tárolóban** ikonra a felső menüben.
6. Kövesse az utasításokat nevű csoport létrehozását **HDInsightUsers**.  Ez a csoport akkor használatos, ha a HDInsight-fürtöt hoz létre az oktatóanyag későbbi részében.

> [!IMPORTANT]
> Az elsődleges virtuális gépet egy tartományhoz csatlakozó HDInsight-fürt létrehozása előtt újra kell indítani.

### <a name="create-an-hdinsight-cluster-in-the-vnet"></a>HDInsight-fürtök létrehozása a Vneten belül

Ebben a szakaszban az Azure-portál hozzáadása a HDInsight-fürtöt létrehozni a virtuális hálózatban, az oktatóanyag során korábban küldje el a Resource Manager sablonnal létrehozott használja. Ez a cikk vonatkozik a tartományhoz csatlakoztatott fürtkonfiguráció kapcsolatos adatok.  Általános információkért lásd: [az Azure portál használatával hdinsight létrehozása Linux-alapú fürtökön](../hdinsight-hadoop-create-linux-clusters-portal.md).  

**A tartományhoz csatlakoztatott HDInsight-fürt létrehozása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg az oktatóanyag során korábban küldje el a Resource Manager sablonnal létrehozott erőforráscsoportot.
3. Adja hozzá a HDInsight-fürtök az erőforráscsoporthoz.
4. Válassza ki **egyéni** lehetőséget:

    ![HDInsight tartományhoz csatlakozó egyéni beállítás létrehozása](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    Az egyéni konfigurációs beállítás használatával hat szakaszok is vannak: alapjai, tárolási, alkalmazás, fürt méretét, speciális beállításai és összegzése.
5. Az a **alapjai** szakasz:

    - Fürt típusa: válassza ki **vállalati biztonsági csomag**. Jelenleg a vállalat biztonsági csomag csak akkor engedélyezhető a következő fürt esetében: Hadoop, interaktív lekérdezés és Spark.

        ![HDInsight tartományhoz csatlakoztatott vállalati biztonsági csomag](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - A fürt bejelentkezési felhasználónevének: Ez az a Hadoop HTTP felhasználó. Ez a fiók eltér a tartományi rendszergazdai fiókot.
    - Erőforráscsoport: válassza ki a korábban a Resource Manager sablonnal létrehozott erőforráscsoportot.
    - Helye: A hely csak azonos legyen a virtuális hálózat létrehozásakor használt és a tartományvezérlők, amelyek a Resource Manager-sablon használatával.

6. Az a **speciális beállítások** szakasz:

    - Domain settings:

        ![HDInsight tartományhoz speciális beállítások tartomány](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - Tartománynév: Adja meg a tartománynevet, [hozzon létre egy Active Directory](#create-an-active-directory).
        - Tartományi felhasználónév: Adja meg az Active Directory-rendszergazda felhasználói nevét használt [hozzon létre egy Active Directory](#create-an-active-directory).
        - Szervezeti egység: A képernyőfelvétel egy vonatkozó példáért lásd:.
        - LDAPS URL-címe: A képernyőfelvétel egy vonatkozó példáért lásd:
        - Hozzáférés felhasználói csoportot: Adja meg a létrehozott felhasználói csoportnév [hozza létre az AD felhasználók és csoportok](#optionally-createad-users-and-groups)
    - Virtuális hálózat: válassza ki a létrehozott virtuális hálózatot [hozzon létre egy Active Directory](#create-an-active-directory). Az alapértelmezett név a sablonban használt **adVNET**.
    - Alhálózati: Alapértelmezés szerint a sablonban használt ez **adSubnet**.



Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. A fürtök törlésével utasításokért lásd: [kezelése Hadoop-fürtök a HDInsight az Azure portál használatával](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>További lépések
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozni a tartományhoz a HDInsight-fürtök, lásd: [SSH használata a HDInsight Linux, Unix vagy OS X, Linux-alapú Hadooppal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

