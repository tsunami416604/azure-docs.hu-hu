---
title: Az Azure Blockchain szolgáltatás korlátai
description: A szolgáltatás és a funkcionális korlátok áttekintése az Azure Blockchain szolgáltatásban
ms.date: 03/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c728e617ac37795988cd596c7cb0c5025aac4ccf
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529571"
---
# <a name="limits-in-azure-blockchain-service"></a>Korlátok az Azure Blockchain szolgáltatásban

Az Azure Blockchain Szolgáltatás rendelkezik szolgáltatási és funkcionális korlátokkal, például a tagok által birtokolható csomópontok számával, a konzorciumi korlátozásokkal és a tárolási mennyiségekkel.

## <a name="pricing-tier"></a>Tarifacsomag

A tranzakciók ra és az érvényesítő csomópontokra vonatkozó maximális korlátok attól függnek, hogy az Azure Blockchain szolgáltatást alapszintű vagy standard szintű tarifacsomagokon építi-e ki.

| Tarifacsomag | Tranzakciócsomópontok maximális száma | Validator-csomópontok maximális száma |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

A konzorciumi hálózatnak legalább két Azure Blockchain Service standard szintű csomóponttal kell rendelkeznie. A standard szintű csomópontok két validátorcsomópontot tartalmaznak. Négy validator csomópontnak kell megfelelnie [az isztambuli bizánci hibatűrési konszenzusnak](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Az alapszint használata fejlesztésre, tesztelésre és a fogalmak igazolására szolgál. Használja a szabványos szint éles szintű központi telepítések. A *Standard* szint akkor is érdemes használnia, ha blockchain data managert használ, vagy nagy mennyiségű magántranzakciót küld.

A tagság létrehozása után nem támogatott a tarifacsomag alap- és szabványos szint közötti módosítása.

## <a name="storage-capacity"></a>Tárkapacitás

A főkönyvi adatokhoz és naplókhoz csomópontonként használható tárterület maximális mennyisége 1,8 terabájt.

A főkönyvi és a naplótár méretének csökkentése nem támogatott.

## <a name="consortium-limits"></a>Konzorciumi korlátok

* **A konzorcium- és tagneveknek egyedinek kell lenniük** az Azure Blockchain szolgáltatásban szereplő más konzorciumi és tagnevekből.

* **A tagok és konzorciumok neve nem módosítható**

* **A konzorcium minden tagjának ugyanabban az árképzési szinten kell lennie**

* **Minden konzorciumban részt vevő tagnak ugyanabban a régióban kell tartózkodnia.**

    A konzorciumban létrehozott első tag diktálja a régiót. A konzorcium meghívott tagjainak ugyanabban a régióban kell lakniuk, mint az első tagnak. Ha minden tagot ugyanarra a régióra korlátozunk, az segít biztosítani, hogy a hálózati konszenzust ne befolyásolja hátrányosan.

* **A konzorciumnak legalább egy rendszergazdával kell rendelkeznie**

    Ha egy konzorciumban csak egy rendszergazda van, akkor nem távolíthatják el magukat a konzorciumból, és nem törölhetik a tagot, amíg egy másik adminisztrátort nem vesznek fel vagy nem léptetnek elő a konzorciumban.

* **A konzorciumból eltávolított tagokat nem lehet újra hozzáadni**

    Inkább újra meg kell hívni őket, hogy csatlakozzanak a konzorciumhoz, és hozzanak létre egy új tagot. Meglévő tagerőforrásaik nem törlődnek a korábbi tranzakciók megőrzése érdekében.

* **A konzorcium minden tagjának ugyanazt a főkönyvi verziót kell használnia.**

    Az Azure Blockchain Szolgáltatásban elérhető javításokról, frissítésekről és főkönyvi verziókról a [Javítás, a frissítések és](ledger-versions.md)a verziók című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

További információ a rendszerek javításával és frissítésével kapcsolatos irányelvekről – [Javítás, frissítések és verziók](ledger-versions.md).
