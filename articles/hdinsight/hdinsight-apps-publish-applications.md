---
title: Azure HDInsight-alkalmazások közzététele |} Microsoft Docs
description: Útmutató a HDInsight-alkalmazások létrehozásához, és tegye közzé az Azure piactéren.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 9c538be4948a8e67ee9b94fcd6ff6bcea04438ef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201706"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>HDInsight-alkalmazások közzététele az Azure piactéren
Azure HDInsight-alkalmazásokat is telepíthető egy Linux-alapú HDInsight-fürtöt. Ebben a cikkben megtudhatja, hogyan tehet közzé HDInsight-alkalmazások az Azure piactéren. Az Azure piactéren közzétételre vonatkozó általános információkért lásd: [ajánlat közzététele az Azure piactéren](../marketplace-publishing/marketplace-publishing-getting-started.md).

A HDInsight-alkalmazások használata a *kapcsolja a saját licenc használata (BYOL)* modell. A BYOL-forgatókönyvek alkalmazás felelős a felhasználók az alkalmazást. Alkalmazás felhasználói van szó, csak az általuk létrehozott, például a HDInsight-fürthöz, és a fürt virtuális gépek és csomópontok Azure-erőforrások. Jelenleg az alkalmazás maga nem történik, az Azure-ban.

További információkért lásd: a HDInsight-alkalmazásokhoz kapcsolódó cikkek:

* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md). Útmutató a HDInsight-alkalmazások telepítése a fürtökön.
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md). Ismerje meg, hogyan kell telepíteni és tesztelni az egyéni HDInsight-alkalmazások.

## <a name="prerequisites"></a>Előfeltételek
Az alkalmazást a piactéren, először küldenie [létrehozása és az egyéni alkalmazás teszteléséhez](hdinsight-apps-install-custom-applications.md).

A fejlesztői fiókját is regisztrálnia. További információkért lásd: [ajánlat közzététele az Azure piactéren](../marketplace-publishing/marketplace-publishing-getting-started.md) és [Microsoft Developer-fiók létrehozása](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-the-application"></a>Az alkalmazás meghatározása
Alkalmazások közzététele a piactéren résztvevő két lépést. Először adja meg a *createUiDef.json* fájlt. A createUiDef.json mintafájl azt jelzi, mely fürtök az alkalmazás kompatibilis. A sablon az Azure-portálon, majd közzététele. Íme egy createUiDef.json mintafájl látható:

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
| versions |Azok a HDInsight-fürttípusok, amelyekkel az alkalmazás kompatibilis. |3.4 |

## <a name="application-installation-script"></a>Alkalmazás telepítési parancsfájlt
Ha az alkalmazás telepítve van egy fürt (vagy egy meglévő fürthöz, vagy egy új), egy élcsomópontot jön létre. Az alkalmazás telepítési parancsfájlt futtatja, a peremhálózati csomóponton.

  > [!IMPORTANT]
  > Az alkalmazás telepítési parancsfájl neve az adott fürt egyedinek kell lennie. A parancsfájl nevének kell lennie a következő formátumban:
  > 
  > "name": "[concat (" hue-install-v0 ','-', uniquestring('applicationName')] "
  > 
  > A parancsfájl nevét három részből áll:
  > 
  > * A parancsfájl nevének előtagja, amelynek tartalmaznia kell az alkalmazás nevét vagy az alkalmazáshoz kapcsolódó nevet.
  > * A kötőjel az olvashatóság érdekében.
  > * Egy egyedi karakterláncfüggvény, a paraméter nevével együtt.
  > 
  > A megőrzött Parancsfájlműveletek listáján, a fenti példában megjelenik, amely szerint **hue-install-v0-4wkahss55hlas**. Tekintse meg a [JSON hasznos](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

A telepítési parancsfájl a következő jellemzőkkel kell rendelkeznie:
* A parancsfájl az idempotent. A parancsfájl több hívásainak ugyanazt az eredményt eredményez.
* A parancsfájl verziószámmal. Amikor frissíti vagy módosítások tesztelését használható a parancsfájl egy másik helyre. Ez biztosítja, hogy az ügyfelek, akik telepíti az alkalmazást nem a frissítések által érintett vagy tesztelési. 
* A parancsfájl minden ponton rendelkezik a megfelelő naplózási. Parancsfájl naplók általában az egyetlen lehetőség az alkalmazás telepítési problémák hibakeresését.
* Külső-szolgáltatások vagy erőforrások hívások rendelkezik a megfelelő újrapróbálkozások, hogy az átmeneti hálózati probléma nincs hatással a telepítés.
* Ha parancsfájlját elindítja a szolgáltatásokat a csomópontokon, szolgáltatások figyeli, és úgy konfigurálva, hogy automatikusan elindul, ha egy csomópont-újraindítás történik.

## <a name="package-the-application"></a>Az alkalmazás becsomagolása
Hozzon létre egy .zip fájlt, amely a HDInsight-alkalmazások telepítéséhez szükséges fájlokat tartalmazza. A zip-fájlt használ [tegye közzé az alkalmazást](#publish-application). A zip-fájlt a következő fájlokat tartalmazza:

* [createUiDefinition.json](#define-application)
* mainTemplate.json (lásd: a minta [egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md).)
* Az összes szükséges parancsfájlok

> [!NOTE]
> Az alkalmazásfájlok (beleértve a webes alkalmazás fájlok) bármely nyilvánosan elérhető végponton tárolhatja.
> 

## <a name="publish-the-application"></a>Az alkalmazás közzététele
HDInsight-alkalmazások közzététele:

1. Jelentkezzen be [Azure közzétételi](https://publish.windowsazure.com/).
2. A bal oldali menüben válassza ki a **megoldás sablonok**.
3. Adjon meg egy címet, majd válassza ki **hozzon létre egy új megoldássablon**.
4. Ha a szervezet már még nem regisztrált, válassza ki a **létrehozás fejlesztői központban regisztrált fiókjában és csatlakozás az Azure programhoz**.  További információkért lásd: [Microsoft Developer-fiók létrehozása](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Válassza ki **megadása a kezdéshez néhány topológiák**. A megoldássablon egy hozzá tartozó topológia "szülőeleme". Egy ajánlat vagy megoldás sablonban több topológiát is megadhat. Amikor egy ajánlatot a rendszer előkészítésre továbbít, topológiájával együtt az összes hozzá tartozó topológia. 
6. Adja meg a topológia nevét, majd válassza ki **+**.
7. Adjon meg egy új verzióra, majd válassza ki **+**.
8. A létrehozott mikor .zip fájl feltöltéséhez, [csomagolva az alkalmazás](#package-application).  
9. Válassza ki **kérjen tanúsítási**. A Microsoft hitelesítő csapata áttekinti a fájlokat, és hitelesíti a topológiát.

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md) a fürtön.
* Megtudhatja, hogyan [egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) és központi telepítése egy közzé nem tett HDInsight-alkalmazást.
* Megtudhatja, hogyan [parancsfájlművelet segítségével testre szabhatja a Linux-alapú HDInsight-fürtök](hdinsight-hadoop-customize-cluster-linux.md) és további alkalmazások felvételéhez. 
* Megtudhatja, hogyan [Linux-alapú Hadoop-fürtök létrehozása a Hdinsightban Azure Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Megtudhatja, hogyan [egy üres élcsomópontot használata a Hdinsightban](hdinsight-apps-use-edge-node.md) hozzáférni a HDInsight-fürtök, a HDInsight-alkalmazások tesztelése, és a HDInsight-alkalmazások futtatni.

