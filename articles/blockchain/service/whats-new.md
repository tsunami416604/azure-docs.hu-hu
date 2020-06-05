---
title: Újdonságok Kibocsátási megjegyzések – Azure Blockchain Service
description: Ismerje meg az Azure Blockchain szolgáltatás újdonságait, például a legújabb kibocsátási megjegyzéseket, a verziókat, az ismert problémákat és a közelgő változásokat.
ms.date: 06/03/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c5316aa387de28fe1a78b336eb2e9e010c624b02
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435428"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Újdonságok az Azure Blockchain szolgáltatásban?

> Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` az RSS-hírcsatorna olvasójának [ ![ RSS-hírcsatorna olvasójának ikonja](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us).

Az Azure Blockchain szolgáltatás folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- Új képességek
- Verziófrissítések
- Ismert problémák

---

## <a name="may-2020"></a>2020. május

### <a name="version-upgrades"></a>Verziófrissítések

- Ubuntu verziófrissítése 18,04-re
- Kvórum verziófrissítése a 2.5.0-re
- A Tessera verziófrissítésének 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Az Azure Blockchain szolgáltatás támogatja a rawPrivate-tranzakciók küldését

**Írja be a következőt:** Vonás

Az ügyfél a fiókon kívül is aláírhat privát tranzakciókat a csomóponton.

### <a name="two-phase-member-provisioning"></a>Kétfázisú tag kiépítés

**Írja be a következőt:** Javítása

Két fázis segít optimalizálni az olyan forgatókönyveket, amelyekben egy tag egy hosszú, meglévő konzorciumban jön létre. A tagsági infrastruktúra az első fázisban van kiépítve. A második fázisban a tag szinkronizálva van a blockchain. A kétfázisú kiépítés segít elkerülni a tagoknak az időtúllépések miatti sikertelen létrehozását.

## <a name="known-issues"></a>Ismert problémák

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>A bányászat leáll, ha kevesebb mint négy érvényesítő csomópont

Az üzemi hálózatoknak legalább négy érvényesítő csomóponttal kell rendelkezniük. A kvórum javasolja, hogy a IBFT összeomlási hibatűrésének (3F + 1) kielégítéséhez legalább négy érvényesítő csomópontra van szükség. Négy érvényesítő csomópont beszerzéséhez legalább két Azure Blockchain Service *standard* szintű csomópontnak kell lennie. A standard csomópontok két érvényesítő csomóponttal vannak kiépítve.  

Ha az Azure Blockchain szolgáltatásban található Blockchain-hálózat nem rendelkezik négy érvényesítő csomóponttal, akkor előfordulhat, hogy a bányászat leáll a hálózaton. A bányászatot a feldolgozott blokkokra vonatkozó riasztás beállításával észlelheti. Egy kifogástalan állapotú hálózatban a feldolgozott blokk öt percenként 60 blokk lesz.

Az Azure Blockchain Service csapata a csomópont újraindítását is csökkenti. Az ügyfeleknek meg kell nyitniuk egy támogatási kérést a csomópont újraindításához. Az Azure Blockchain Service csapata a bányászati problémák automatikus észlelésére és kijavítására dolgozik.

Használja a *standard* szintű üzemi szintű üzembe helyezést. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>A Blockchain Data Manager standard szintű csomópontot igényel

Ha Blockchain Data Manager használ, használja a *standard* szintet. Az alapszintű *csomag* csak 4 GB memóriát tartalmaz. Ezért nem lehet méretezni a Blockchain-Data Manager és a rajta futó egyéb szolgáltatások által igényelt használatra.

A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Nagy mennyiségű feloldási fiók hívása geth okoz

Nagy mennyiségű feloldási fiók hívása a tranzakció elküldésekor a tranzakciós csomópont geth összeomlását okozhatja. Ennek eredményeképpen le kell állítania a tranzakciók betöltését. Ellenkező esetben a függőben lévő tranzakciós várólista növekszik.

A geth egy percen belül automatikusan újraindul. A csomóponttól függően a szinkronizálás hosszú időt is igénybe vehet. Az Azure Blockchain Service csapata olyan archiválási funkciót tesz lehetővé, amely csökkenti a szinkronizálás időpontját.

A geth összeomlások azonosításához az Blockchain üzeneteiben lévő hibaüzenetek naplóit is megtekintheti. Azt is megtekintheti, hogy a feldolgozott blokkok csökkentik-e a függőben lévő tranzakciók növekedését.

A probléma megoldásához a fiók zárolásának feloldásához egy paranccsal küldje el az aláírt tranzakciókat az aláíratlan tranzakciók küldése helyett. A már külsőleg aláírt tranzakciók esetében nincs szükség a fiók zárolásának feloldására.

Ha aláíratlan tranzakciókat szeretne küldeni, a feloldási parancsban a 0 értékre való küldéssel oldja fel a fiók végtelen idejét. Az összes tranzakció elküldése után visszaállíthatja a fiókot.  

Az Azure Blockchain szolgáltatás által használt geth paraméterek a következők: Ezek a paraméterek nem módosíthatók.

- Isztambuli blokk időtartama: 5 MP
- Maximális méret: 700000000
- Ceil-gáz korlátja: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>A privát tranzakciók nagy mennyisége csökkenti a teljesítményt

Ha az Azure Blockchain szolgáltatás alapszintű és privát tranzakcióit használja, a Tessera összeomlást eredményezhet.

A Tessera-összeomlás észleléséhez tekintse át a Blockchain-alkalmazás naplóit, és keresse meg az üzenetet `Tessera crashed. Restarting Tessera...` .

Az Azure Blockchain szolgáltatás összeomlás esetén újraindítja a Tesserat. Az újraindítás körülbelül egy percet vesz igénybe.

Ha nagy mennyiségű privát tranzakciót küld, használja a *standard* szintet. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

### <a name="calling-ethestimate-gas-function-reduces-performance"></a>Az ETH meghívása

Az *ETH* meghívása többször is csökkenti a tranzakciók másodpercenkénti számát. Ne használja az *ETH. költségbecslés* Gas függvényt az egyes tranzakciók beküldéséhez. Az *ETH. becslési* függvény a memória intenzív.

Ha lehetséges, használjon egy konzervatív gáz értéket a tranzakciók elküldéséhez, és csökkentse az *ETH. becslés*használatát.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Az intelligens szerződések nem kötött hurkoi csökkentik a teljesítményt

Kerülje a nem kötött hurkokat az intelligens szerződésekben, mivel azok csökkenthetik a teljesítményt. További információkért lásd a következőket:

- [A nem kötött hurkok elkerülése](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Az intelligens szerződéssel kapcsolatos ajánlott biztonsági eljárások](https://github.com/ConsenSys/smart-contract-best-practices)
- [A kvórum által biztosított intelligens szerződési irányelvek](http://docs.goquorum.com/en/latest/Security/Framework/Decentralized%20Application/Smart%20Contracts%20Security/)
- [Irányelvek a szilárdtestek által biztosított gázárak és hurkok esetében](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)