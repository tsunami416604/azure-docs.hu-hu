---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85114534"
---

## <a name="using-this-example-knowledge-base"></a>A példa Tudásbázis használata

Az ebben a rövid útmutatóban szereplő Tudásbázis 2 társalgási QnA-párral kezdődik, így egyszerűsíthető a példa, és a frissítési metódusban a gyors előre jelezhető azonosítók használhatók, és a követő kérdéseket új párokkal társítjuk. Ez a rövid útmutatóhoz megadott sorrendben lett megtervezve és implementálva.

Ha azt tervezi, hogy idővel fejleszti a tudásbázist a meglévő QnA-pároktól függő követő utasításokkal, a következők közül választhat:
* A nagyobb tudásbázisok esetében a tudásbázist egy szövegszerkesztőben vagy TSV-eszközben kezelheti, amely támogatja az automatizálást, majd egy frissítéssel egyszer teljesen lecseréli a tudásbázist.
* A kisebb tudásbázisok esetében a követő utasításokat teljes mértékben a QnA Maker portálon kezelheti.

Az ebben a rövid útmutatóban használt QnA párok részletei:
* QnA pár típusa – ebben a Tudásbázisban két típusú QnA-pár létezik, a frissítés után: chitchat és a tartományra vonatkozó információk. Ez akkor jellemző, ha a Tudásbázis egy beszélgetési alkalmazáshoz (például egy Csevegőrobot) van kötve.
* A Tudásbázis válaszait metaadatok alapján vagy a követési kérések használatával lehet szűrni, ez a rövid útmutató nem mutatja ezt. Itt megkeresheti az [itt](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)található nyelv-agnosztikus generateAnswer-példákat.
* A Markdown és a [Markdown széles választékát](../reference-markdown-format.md) , például a képeket (nyilvánosan elérhető internetalapú lemezképeket), a hivatkozásokat (a nyilvánosan elérhető URL-címekre) és a felsorolásjeles pontokat is tartalmazhatja, ez a rövid útmutató nem használja ezt a fajtát.
