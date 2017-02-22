---
title: "HDInsight-alkalmazások közzététele | Microsoft Docs"
description: "További információk a HDInsight-alkalmazások létrehozásáról és közzétételéről."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 35a6c06bc4850f3fcfc6221d62998465f3b38251


---
# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>HDInsight-alkalmazások közzététele az Azure Piactéren
A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései. Ebből a cikkből megtudhatja, hogyan tehet közzé HDInsight-alkalmazásokat az Azure Piactéren.  Az Azure Piactéren történő közzétételre vonatkozó általános információkat lásd a cikkben, amely azzal foglalkozik, [hogyan lehet ajánlatot közzétenni az Azure Piactéren](../marketplace-publishing/marketplace-publishing-getting-started.md).

A HDInsight-alkalmazások a *saját licenc használata (BYOL)* modellt használják, amelyben az alkalmazás szolgáltatója felel az alkalmazást licenceléséért a végfelhasználók számára, és az Azure a végfelhasználóknak csak az általuk létrehozott erőforrásokért számol fel költséget, például a HDInsight-fürtért és a hozzá tartozó virtuális gépekért/csomópontokért. Jelenleg az alkalmazás használati díjának felszámolása nem az Azure szolgáltatáson keresztül történik.

A HDInsight-alkalmazásokhoz kapcsolódó további cikkek:

* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Egyéni HDInsight-alkalmazások telepítése): útmutató az egyéni HDInsight-alkalmazások telepítéséhez és teszteléséhez.

## <a name="prerequisites"></a>Előfeltételek
Egyéni alkalmazást úgy küldhet el a piactérre, ha előtte létrehozta és tesztelte az alkalmazást. Lásd az alábbi cikkeket:

* [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Egyéni HDInsight-alkalmazások telepítése): útmutató az egyéni HDInsight-alkalmazások telepítéséhez és teszteléséhez.

A fejlesztői fiókját is regisztrálnia kell. Lásd: [publish an offer to the Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) (Ajánlat közzététele az Azure Piactéren) és [Create a Microsoft Developer account](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md) (Microsoft Developer-fiók létrehozása).

## <a name="define-application"></a>Alkalmazás meghatározása
Az alkalmazások két lépésben tehetők közzé az Azure Piactéren.  Először meg kell adni egy **createUiDef.json** fájlt, amely meghatározza, hogy az alkalmazás melyik fürtökkel legyen kompatibilis, ezután pedig közzé kell tenni a sablont az Azure Portalról. Az alábbiakban egy createUiDef.json mintafájl látható.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| Mező | Leírás | Lehetséges értékek |
| --- | --- | --- |
| types |Azok a fürttípusok, amelyekkel az alkalmazás kompatibilis. |Hadoop, HBase, Storm, Spark (vagy ezek kombinációi) |
| tiers |Azok a fürtrétegek, amelyekkel az alkalmazás kompatibilis. |Standard, Premium (vagy mindkettő) |
| versions |Azok a HDInsight-fürttípusok, amelyekkel az alkalmazás kompatibilis. |3.4 |

## <a name="package-application"></a>Alkalmazás becsomagolása
Hozzon létre egy zip fájlt, amely tartalmazza a HDInsight-alkalmazások telepítéséhez szükséges összes fájlt. A zip fájlra az [alkalmazás közzétételéhez](#publish-application) van szükség.

* [createUiDefinition.json](#define-application).
* mainTemplate.json. Az [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Egyéni HDInsight-alkalmazások telepítése) részben megtekinthet egy mintát.
  
  > [!IMPORTANT]
  > Az alkalmazás telepítési parancsfájljainak egyedi névvel kell rendelkezniük egy adott fürtben, és az alábbi formátumúnak kell lenniük. Emellett minden telepítési és eltávolítási szkriptműveletnek idempotentnek kell lennie, azaz a szkripteknek ismételten hívhatóknak kell lenniük, és ugyanazt az eredményt kell adniuk.
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > A parancsfájl nevét három rész alkotja:
  > 
  > 1. A parancsfájl nevének előtagja, amelynek tartalmaznia kell az alkalmazás nevét vagy egy, az alkalmazáshoz kapcsolódó nevet.
  > 2. Egy „-” kötőjel az olvashatóság kedvéért.
  > 3. Egy egyedi karakterláncfüggvény, amelynek a paramétere az alkalmazás neve.
  > 
  > Például a fentiből a következő lesz a megőrzött parancsfájlműveletek listáján: hue-install-v0-4wkahss55hlas. A JSON hasznos adatokra a következő helyen talál példát: [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 
  > 
* Minden kötelező parancsfájl.

> [!NOTE]
> Az alkalmazás fájljai (köztük az esetleges webalkalmazások fájljai) bármilyen nyilvánosan elérhető végponton lehetnek.
> 
> 

## <a name="publish-application"></a>Az alkalmazás közzététele
A HDInsight-alkalmazások közzétételéhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure közzétételi portáljára](https://publish.windowsazure.com/).
2. Új megoldássablon létrehozásához kattintson a bal oldalon létható **Solution templates** (Megoldássablonok) gombra.
3. Adjon meg egy címet, és kattintson **Create a new solution template** (Új megoldássablon létrehozása) elemre.
4. Kattintson a **Create Dev Center account and join the Azure program** (Fiók létrehozása a Fejlesztői Központban és csatlakozás az Azure programhoz) lehetőségre, és regisztrálja a vállalatát, ha ezt még nem tette meg.  Lásd: [Create a Microsoft Developer account](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md) (Microsoft Developer-fiók létrehozása).
5. Kattintson a **Define some Topologies to get Started** (Topológiák megadása a kezdéshez) elemre. A megoldássablon az összes hozzá tartozó topológia „szülőeleme”. Egy ajánlat/megoldássablonban több topológiát is megadhat. Amikor egy ajánlatot a rendszer előkészítésre továbbít, az összes topológiájával együtt továbbítja. 
6. Adja meg a topológia nevét, majd kattintson a pluszjelre.
7. Adja meg az új verziót, majd kattintson a pluszjelre.
8. Töltse fel az [Alkalmazás becsomagolása](#package-application) szakaszban előkészített zip fájlt.  
9. Kattintson a **Request Certification** (Tanúsítvány kérése) gombra. A Microsoft hitelesítő csapata áttekinti a fájlokat, és hitelesíti a topológiát.

## <a name="next-steps"></a>Következő lépések
* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Egyéni HDInsight-alkalmazások telepítése): megtudhatja, hogyan helyezhet üzembe a HDInsight szolgáltatásban egy olyan HDInsight-alkalmazást, amely nincs közzétéve.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Create Linux-based Hadoop clusters in HDInsight using Azure Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban Azure Resource Manager-sablonok segítségével): Megtudhatja, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.




<!--HONumber=Dec16_HO1-->


