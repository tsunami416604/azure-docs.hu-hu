---
title: Azure HDInsight-alkalmazások közzététele
description: Ismerje meg, hogyan hozhat létre egy HDInsight-alkalmazást, majd teheti közzé az Azure Piactéren.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e64bf253a73df3a2f8170109dc1dfb9a59613733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64685321"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>HDInsight-alkalmazás közzététele az Azure Piactéren
Telepíthet egy Azure HDInsight-alkalmazást egy Linux-alapú HDInsight-fürtre. Ebből a cikkből megtudhatja, hogyan tehet közzé egy HDInsight-alkalmazást az Azure Piactéren. Az Azure Marketplace-en való közzétételről az [Ajánlat közzététele az Azure Piactéren](../marketplace/marketplace-publishers-guide.md)című témakörben talál általános tudnivalókat.

A HDInsight-alkalmazások a *Bring Your Own License (BYOL) modellt* használják. BYOL-forgatókönyv esetén az alkalmazásszolgáltató felelős az alkalmazás felhasználói számára történő licencelésért. Az alkalmazásfelhasználóknak csak az általuk létrehozott Azure-erőforrásokért kell fizetniük, például a HDInsight-fürtért, valamint a fürt virtuális gépeiért és csomópontjaiért. Jelenleg maga az alkalmazás számlázása nem fordul elő az Azure-ban.

További információt a HDInsight alkalmazásokkal kapcsolatos alábbi cikkeiben talál:

* [HdInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md). Ismerje meg, hogyan telepíthet HDInsight-alkalmazásokat a fürtökre.
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md). Ismerje meg, hogyan telepítheti és tesztelheti az egyéni HDInsight-alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek
Az egyéni alkalmazás Marketplace-en való elküldéséhez először [hozza létre és tesztelje az egyéni alkalmazást.](hdinsight-apps-install-custom-applications.md)

A fejlesztői fiókot is regisztrálnia kell. További információt az [Ajánlat közzététele az Azure Piactéren](../marketplace/marketplace-publishers-guide.md) és a Microsoft [Developer-fiók létrehozása](../marketplace/marketplace-publishers-guide.md)című témakörben talál.

## <a name="define-the-application"></a>Az alkalmazás meghatározása
Két lépésben vesznek részt az alkalmazások közzétételében a Marketplace-en. Először definiáljon egy *createUiDef.json* fájlt. A createUiDef.json fájl jelzi, hogy az alkalmazás mely fürtökkel kompatibilis. Ezután tegye közzé a sablont az Azure Portalon. Íme egy példa createUiDef.json fájl:

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
| types |Azok a fürttípusok, amelyekkel az alkalmazás kompatibilis. |Hadoop, HBase, Storm, Spark (vagy ezek bármilyen kombinációja) |
| versions |Azok a HDInsight-fürttípusok, amelyekkel az alkalmazás kompatibilis. |3.4 |

## <a name="application-installation-script"></a>Alkalmazástelepítési parancsfájl
Ha egy alkalmazás telepítve van egy fürtre (vagy egy meglévő fürtre, vagy egy újra), egy peremcsomópont jön létre. Az alkalmazástelepítési parancsfájl a peremhálózati csomóponton fut.

  > [!IMPORTANT]  
  > Az alkalmazástelepítési parancsfájl nevének egyedinek kell lennie egy adott fürthöz. A parancsfájl nevének a következő formátumúnak kell lennie:
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring('applicationName')]"
  > 
  > A parancsfájl neve három részből áll:
  > 
  > * Parancsfájlnév-előtag, amelynek tartalmaznia kell az alkalmazás nevét vagy az alkalmazásra vonatkozó nevet.
  > * Kötőjel, az olvashatóság érdekében.
  > * Egy egyedi karakterlánc-függvény, amelynek az alkalmazás neve a paraméter.
  > 
  > A megőrzött parancsfájl-műveletlistában az előző példa **színárnyalat-install-v0-4wkahss55hlas**néven jelenik meg. Lásd [a minta JSON hasznos teher](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

A telepítési parancsfájlnak a következő jellemzőkkel kell rendelkeznie:
* A forgatókönyv idempotent. A parancsfájl több hívása ugyanazt az eredményt eredményezi.
* A parancsfájl megfelelőverzióval van elferdítve. A módosítások frissítésekor vagy tesztelésekor használjon másik helyet a parancsfájlhoz. Ez biztosítja, hogy az alkalmazást telepítő ügyfeleket ne érintsék a frissítések vagy a tesztelés. 
* A parancsfájl minden ponton megfelelő naplózással rendelkezik. Általában a parancsfájlnaplók az egyetlen módja annak, hogy hibakeresési alkalmazás telepítési problémákat.
* A külső szolgáltatások vagy erőforrások hívásai megfelelő újrapróbálkozásokkal rendelkeznek, így a telepítést nem érintik az átmeneti hálózati problémák.
* Ha a parancsfájl elindítja a csomópontokon lévő szolgáltatásokat, a rendszer figyeli és konfigurálja, hogy automatikusan elinduljanak, ha egy csomópont újraindul.

## <a name="package-the-application"></a>Csomagolja be az alkalmazást
Hozzon létre egy .zip fájlt, amely tartalmazza a HDInsight-alkalmazás telepítéséhez szükséges összes fájlt. A .zip fájl segítségével teheti közzé az alkalmazást. A .zip fájl a következő fájlokat tartalmazza:

* createUiDefinition.json
* mainTemplate.json (Minta: [Egyéni HDInsight-alkalmazások telepítése.)](hdinsight-apps-install-custom-applications.md)
* Minden szükséges parancsfájl

> [!NOTE]  
> Az alkalmazásfájlokat (beleértve a webalkalmazás-fájlokat is) bármely nyilvánosan elérhető végponton üzemeltetheti.

## <a name="publish-the-application"></a>Az alkalmazás közzététele
HDInsight-alkalmazás közzététele:

1. Jelentkezzen be az [Azure Publishing szolgáltatásba.](https://publish.windowsazure.com/)
2. A bal oldali menüben válassza a **Megoldássablonok lehetőséget.**
3. Írjon be egy címet, és válassza **az Új megoldássablon létrehozása lehetőséget.**
4. Ha még nem regisztrálta a szervezetet, válassza **a Fejlesztői központ létrehozása fiók lehetőséget, és csatlakozzon az Azure-programhoz.**  További információt a [Microsoft Developer-fiók létrehozása című témakörben talál.](../marketplace/marketplace-publishers-guide.md)
5. Az első lépések megkezdéséhez válassza **a Topológiák definiálása**lehetőséget. A megoldássablon minden topológiájának "szülője". Egy ajánlatban vagy megoldássablonban több topológiát is definiálhat. Amikor egy ajánlatot átmeneti állapotba kerül, a topológiák kalegyütt leküldéses lesz. 
6. Írjon be egy topológianevet, majd válassza a lehetőséget. **+**
7. Adjon meg egy új **+** verziót, majd válassza a lehetőséget.
8. Töltse fel az alkalmazás csomagolásakor létrehozott .zip fájlt.  
9. Válassza **a Tanúsítványkérése lehetőséget.** A Microsoft minősítő csapata áttekinti a fájlokat, és hitelesíti a topológiát.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan telepítheti a [HDInsight-alkalmazásokat](hdinsight-apps-install-applications.md) a fürtökben.
* Ismerje meg, hogyan [telepíthet egyéni HDInsight-alkalmazásokat,](hdinsight-apps-install-custom-applications.md) és hogyan telepíthet közzé nem tett HDInsight-alkalmazásokat a HDInsightba.
* Ismerje meg, hogyan [használhatja a Parancsfájl-művelet et a Linux-alapú HDInsight-fürtök testreszabására](hdinsight-hadoop-customize-cluster-linux.md) és további alkalmazások hozzáadására. 
* Ismerje meg, hogyan [hozhat létre Linux-alapú Apache Hadoop-fürtöket a HDInsightban az Azure Resource Manager-sablonok használatával.](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* Ismerje meg, hogyan [használhat üres peremhálózati csomópontot a HDInsight-fürtök](hdinsight-apps-use-edge-node.md) eléréséhez, a HDInsight-alkalmazások teszteléséhez és a HDInsight-alkalmazások üzemeltetéséhez.

