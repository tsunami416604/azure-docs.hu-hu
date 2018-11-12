---
title: Az Azure HDInsight-alkalmazások közzététele
description: Ismerje meg, hogyan hozhat létre egy HDInsight-alkalmazás, és közzé kell tennie az Azure piactéren.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 4206301296c94bd0d24743854756ac6dd1d712dd
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014329"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Egy HDInsight-alkalmazás közzététele az Azure Marketplace-en
Egy Azure HDInsight alkalmazást is telepítheti egy Linux-alapú HDInsight-fürtön. Ebből a cikkből megtudhatja, hogyan HDInsight alkalmazás közzététele az Azure piactéren. Az Azure Marketplace közzétételi kapcsolatos általános információkért lásd: [-ajánlat közzététele az Azure Marketplace-en](../marketplace/marketplace-publishers-guide.md).

HDInsight-alkalmazások használata a *Bring Your saját licences (BYOL)* modell. A BYOL forgatókönyvekben egy alkalmazás szolgáltatója felelős az alkalmazást. alkalmazás felhasználóinak. Alkalmazás felhasználóinak csak azok létrehozni, például a HDInsight-fürt és a fürt virtuális gépek vagy csomópontok az Azure-erőforrások díjkötelesek. Jelenleg maga az alkalmazás használati díjának felszámolása nem következik be, az Azure-ban.

További információkért tekintse meg a HDInsight-alkalmazásokhoz kapcsolódó cikkek:

* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md). Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtökön.
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md). Ismerje meg, hogyan telepítheti és tesztelheti az egyéni HDInsight-alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek
A Marketplace-en, először is egyéni jelentkezése elküldésekor [létrehozása és az egyéni alkalmazás teszteléséhez](hdinsight-apps-install-custom-applications.md).

A fejlesztői fiókkal is kell regisztrálni. További információkért lásd: [-ajánlat közzététele az Azure Marketplace-en](../marketplace/marketplace-publishers-guide.md) és [hozzon létre egy Microsoft Developer-fiókot](../marketplace/marketplace-publishers-guide.md).

## <a name="define-the-application"></a>Az alkalmazás meghatározása
Két lépést is igényel, az alkalmazás-közzététel a piactéren. Először határozzon meg egy *createUiDef.json* fájlt. A createUiDef.json mintafájl látható azt jelzi, mely a fürtök az alkalmazás kompatibilis. Majd tegye közzé a sablont az Azure Portalról. Íme egy createUiDef.json mintafájl látható:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Mező | Leírás | Lehetséges értékek |
| --- | --- | --- |
| types |Azok a fürttípusok, amelyekkel az alkalmazás kompatibilis. |Hadoop, HBase, Storm, Spark (vagy ezek kombinációi) |
| verziók |Azok a HDInsight-fürttípusok, amelyekkel az alkalmazás kompatibilis. |3.4 |

## <a name="application-installation-script"></a>Alkalmazás telepítési parancsfájlt
Ha az alkalmazás telepítve van egy fürtön (vagy egy meglévő fürthöz, vagy egy új), egy élcsomópontot jön létre. Az alkalmazás telepítési parancsfájlt futtat az élcsomóponthoz.

  > [!IMPORTANT]
  > Az alkalmazás telepítési parancsfájl neve az adott fürt egyedinek kell lennie. A szkript neve a következő formátumban kell rendelkeznie:
  > 
  > "name": "[concat (" hue-install-v0 ','-', uniquestring('applicationName')] "
  > 
  > A parancsfájl nevét három részből áll:
  > 
  > * A parancsfájl nevének előtagja, amelynek tartalmaznia kell az alkalmazás nevét és a egy, az alkalmazáshoz kapcsolódó nevet.
  > * Egy kötőjel az olvashatóság érdekében.
  > * Egy egyedi karakterláncfüggvény, paramétere az alkalmazás nevével együtt.
  > 
  > A megőrzött Parancsfájlműveletek listáján, az az előző példában látható **hue-install-v0-4wkahss55hlas**. Tekintse meg a [JSON hasznosadat-minta](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

A telepítési parancsfájl a következő jellemzőkkel kell rendelkeznie:
* A szkript idempotens. A parancsfájl több alkalommal hívnia elő ugyanazt az eredményt.
* A parancsfájl verziószámmal. Ha frissíti vagy módosítások teszteléshez használható a parancsfájl egy másik helyet. Ez biztosítja, hogy azokat a felhasználókat, akik az alkalmazást telepíti, nem a frissítések által érintett vagy tesztelési. 
* A szkript rendelkezik a megfelelő naplózási minden pontján. Parancsfájl-naplók általában csak úgy alkalmazás telepítési kapcsolatos problémák megoldásában.
* A külső szolgáltatásokhoz és erőforrásokhoz hívásaihoz megfelelő próbálkozások, hogy a telepítés nem átmeneti hálózati hibák által érintett.
* A szkript elindítja a szolgáltatásokat a csomópontokon, ha szolgáltatások figyeli, és úgy konfigurálva, hogy automatikusan elindul, ha egy csomópont-újraindítás történik.

## <a name="package-the-application"></a>Az alkalmazás becsomagolása
Hozzon létre egy .zip fájlt, amely tartalmazza a HDInsight-alkalmazás telepítéséhez szükséges összes fájlt. A zip-fájlt használ [az alkalmazás közzététele](#publish-application). A zip-fájlt a következő fájlokat tartalmazza:

* [createUiDefinition.json](#define-application)
* mainTemplate.json (egy minta: [egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md).)
* Az összes szükséges szkriptek

> [!NOTE]
> Az alkalmazás fájljai (beleértve a webes alkalmazás fájlok) bármilyen nyilvánosan elérhető végponton is üzemeltethet.
> 

## <a name="publish-the-application"></a>Az alkalmazás közzététele
Egy HDInsight-alkalmazás közzététele:

1. Jelentkezzen be a [Azure-beli közzétételt](https://publish.windowsazure.com/).
2. A bal oldali menüben válassza ki a **megoldássablonok**.
3. Adjon meg egy címet, és válassza ki **új megoldássablon létrehozásához**.
4. Ha már a szervezete még nem regisztrált, válassza ki a **létrehozása fejlesztői központban lévő fiókjához és csatlakozás az Azure-program**.  További információkért lásd: [hozzon létre egy Microsoft Developer-fiókot](../marketplace/marketplace-publishers-guide.md).
5. Válassza ki **megadása a kezdéshez néhány topológiák**. A megoldássablon az összes hozzá tartozó topológia "szülője". Egy ajánlat vagy megoldás sablonban több topológiát is megadhat. Amikor a rendszer továbbítja az ajánlat átmeneti, azt az összes hozzá tartozó topológia leküldésekor. 
6. Adja meg a topológia nevét, és válassza ki **+**.
7. Adja meg egy új verziót, és válassza ki **+**.
8. Töltse fel a .zip-fájlt, akkor meg kell létrehozni, [csomagolva az alkalmazás](#package-application).  
9. Válassza ki **kérjen minősítést**. A Microsoft hitelesítő csapata áttekinti a fájlokat, és hitelesíti a topológiát.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md) a fürtben.
* Ismerje meg, hogyan [egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) és a HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* Ismerje meg, hogyan [Linux-alapú HDInsight-fürtök testreszabása Szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md) és további alkalmazásokat. 
* Ismerje meg, hogyan [HDInsight Linux-alapú Hadoop-fürtök létrehozása az Azure Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Ismerje meg, hogyan [üres élcsomópontot használni a HDInsight](hdinsight-apps-use-edge-node.md) HDInsight-fürtök elérésére, HDInsight-alkalmazások tesztelése és HDInsight-alkalmazások üzemeltetésére.

