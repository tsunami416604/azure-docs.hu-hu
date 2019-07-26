---
title: Riasztások vizsgálata az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan vizsgálja ki a riasztásokat az Azure Sentinel használatával.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: ad9c752898733286701db2d0f0b1fc40029b7521
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370710"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Oktatóanyag: Fenyegetések észlelése az Azure Sentinel előzetes verziójával

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Miután [csatlakoztatta az](quickstart-onboard.md) adatforrásokat az Azure Sentinel szolgáltatáshoz, értesítést szeretne kapni, ha valamilyen gyanús esemény történik. Ennek lehetővé tételéhez az Azure Sentinel lehetővé teszi, hogy speciális riasztási szabályokat hozzon létre, amelyek olyan eseteket állítanak elő, amelyek a környezetében felhasználható rendellenességek és fenyegetések mélyebb kivizsgálásához rendelhetők. 

Ez az oktatóanyag segítséget nyújt az Azure Sentinel-fenyegetések észlelésében.
> [!div class="checklist"]
> * Észlelési szabályok létrehozása
> * Fenyegetési válaszok automatizálása

## <a name="create-detection-rules"></a>Észlelési szabályok létrehozása

Az esetek kivizsgálásához először létre kell hoznia az észlelési szabályokat. 

> [!NOTE]
> Az Azure Sentinelben létrehozott riasztások [Microsoft Graph biztonságon](https://aka.ms/securitygraphdocs)keresztül érhetők el. További részletekért és integrációs partnereinkért tekintse meg a [Microsoft Graph Security riasztások dokumentációját](https://aka.ms/graphsecurityreferencebetadocs) .

Az észlelési szabályok azon veszélyforrások és rendellenességek típusain alapulnak, amelyek gyanúsak lehetnek az Ön környezetében, amelyet azonnal tudni szeretne, így biztosítva a felszínre, a vizsgálatra és a szervizelésre. 

1. Az Azure Sentinel alatti Azure Portal válassza az **elemzés**lehetőséget.

   ![Elemzés](./media/tutorial-detect-threats/alert-rules.png)

2. A felső menüsorban kattintson a **+ Hozzáadás**elemre.  

   ![Riasztási szabály létrehozása](./media/tutorial-detect-threats/create-alert-rule.png)

3. A **riasztási szabály létrehozása**területen adjon meg egy leíró nevet, és szükség szerint  állítsa be a súlyosságot. 

4. Hozza létre a lekérdezést Log Analyticsban, majd illessze be a **riasztási szabály beállítása** mezőbe. Íme egy példa a lekérdezésre, amely riasztást küld, ha rendellenes számú erőforrás jön létre az Azure-tevékenységben.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > A lekérdezés hosszának 1 és 10000 karakter közöttinek kell lennie, és nem tartalmazhat "Search *" és "Union *" karaktert.


5. Az **entitás-hozzárendelés** szakaszban az **entitás típusa** területen található mezők használatával rendelje hozzá a lekérdezésben szereplő oszlopokat az Azure Sentinel által felismert entitás mezőihez. Az egyes mezőkhöz rendelje hozzá a Log Analyticsban létrehozott lekérdezés megfelelő oszlopát a megfelelő entitás mezőhöz. Válassza ki a megfelelő oszlopnevet a **tulajdonság**alatt. Minden entitás több mezőt tartalmaz (például SID, GUID stb.). Az entitást a mezők bármelyike szerint leképezheti, nem csak a felső szintű entitást.

6. Riasztási trigger feltételeinek meghatározása a **riasztási trigger**alatt. Ez határozza meg a riasztást kiváltó feltételeket. 

7. Állítsa be  , hogy a lekérdezés milyen gyakran fusson – akár 5 percenként, akár ritkábban, akár naponta egyszer. 

8. Állítsa be  azt az időtartamot, amellyel szabályozható, hogy a lekérdezés mennyi adattal fusson, például óránként, 60 percen belül.

9. Megadhatja az elnyomást is. A tiltás akkor lehet hasznos, ha le szeretné állítani az ismétlődő riasztások aktiválását ugyanazon incidens esetén. Így leállíthatja a riasztásokat egy adott időszakban. Ez segít elkerülni az incidensek ismétlődő riasztásait, és lehetővé teszi az egymást követő riasztások kihagyását egy adott időszakra vonatkozóan. Ha például a **riasztás ütemezési** **gyakorisága** 60 percre van beállítva, és a **riasztások ütemezési időtartama** két óra, és a lekérdezés eredményei meghaladják a meghatározott küszöbértéket, akkor az első észlelése után a rendszer kétszer riasztást indít el. az elmúlt 60 percben, majd ismét, ha a minta 2 órás adat első 60 percében szerepel. Javasoljuk, hogy ha riasztást vált ki, a tiltásnak a riasztási időszakban beállított időtartamra kell esnie. A példánkban érdemes lehet letiltani a 60 percet, hogy a riasztások csak a legutóbbi órában történt események esetén legyenek aktiválva.

8. Miután beillesztette a lekérdezést a **riasztási szabály beállítása** mezőbe, azonnal megtekintheti a riasztás szimulálása a **logikai riasztás szimulálása** alatt, így megtudhatja, hogy mennyi adat jön létre a riasztás adott időintervallumában. Ön által létrehozott. Ez attól függ, hogy mire van beállítva a **gyakoriság** és a **küszöbérték**. Ha úgy látja, hogy a riasztások átlaga túl gyakran aktiválódik, érdemes megadnia az eredmények számát, hogy az az átlagosnál nagyobb legyen.

9. A riasztási szabály inicializálásához kattintson a **Létrehozás** gombra. A riasztás létrehozása után létrejön egy eset, amely tartalmazza a riasztást. A meghatározott észlelési szabályokat sorokként tekintheti meg a **Security Analytics** lapon. Megtekintheti az egyes szabályokhoz tartozó egyezések számát is – az elindított riasztásokat. Ebből a listából engedélyezheti, letilthatja vagy törölheti az egyes szabályokat. Azt is megteheti, hogy a sor végén lévő három pontra (...) kattint az egyes riasztások szerkesztéséhez, letiltásához, klónozásához, a találatok megjelenítéséhez vagy a szabály törléséhez. Az **elemzési** oldal az összes aktív riasztási szabály gyűjteménye, beleértve az Ön által engedélyezett sablonokat és a sablonok alapján létrehozott riasztási szabályokat.

1. A riasztási szabályok eredményei az **esetek** oldalon láthatók, ahol az osztályozás, az [esetek kivizsgálására](tutorial-investigate-cases.md)és a fenyegetések elhárítására van lehetőség.



## <a name="automate-threat-responses"></a>Fenyegetési válaszok automatizálása

SIEM/SOC-csapatok rendszeres időközönként biztonsági riasztásokkal is elárasztható. A generált riasztások mennyisége annyira nagy, hogy a rendelkezésre álló biztonsági rendszergazdák túlterheltek. Ez az eredmény túl gyakran olyan helyzetekben, amikor sok riasztást nem lehet megvizsgálni, így a szervezet sebezhetővé válik a nem felmerülő támadásokkal szemben. 

A riasztások többsége – ha nem a legtöbb esetben – megfelel az ismétlődő mintázatoknak, amelyeket meghatározott és meghatározott szervizelési műveletek kezelhetnek. Az Azure Sentinel már lehetővé teszi a szervizelés megadását a forgatókönyvekben. Azt is megteheti, hogy valós idejű automatizálást állít be a forgatókönyv-definíció részeként, így lehetővé teszi, hogy a meghatározott biztonsági riasztásokra adott válasz teljes mértékben automatizálható legyen. A valós idejű automatizálással a válaszokkal rendelkező csapatok jelentősen csökkenthetik a számítási feladatokat azáltal, hogy teljes mértékben automatizálják a rendszeres válaszokat a riasztások ismétlődő típusaira, így többek között az egyedi riasztásokra koncentrálhat, elemezheti a mintákat, a veszélyforrások elleni vadászatot és egyebeket.

Válaszok automatizálása:

1. Válassza ki azt a riasztást, amelynek a válaszát automatizálni szeretné.
1. Az Azure Sentinel munkaterület navigációs menüjében válassza az **elemzés**lehetőséget.
1. Válassza ki az automatizálni kívánt riasztást. 
1. A **riasztási szabály szerkesztése** lap **valós idejű automatizálás**területén válassza ki azt az **aktivált** forgatókönyvet, amelyet futtatni szeretne a riasztási szabály egyeztetése során.
1. Kattintson a **Mentés** gombra.

   ![valós idejű automatizálás](./media/tutorial-detect-threats/rt-configuration.png)


Emellett manuálisan is kijavíthat egy riasztást, ha a riasztáson belül futtat egy forgatókönyvet, ehhez kattintson a forgatókönyvek **megtekintése** elemre, majd válassza ki a futtatandó forgatókönyvet. További információ az új forgatókönyvek létrehozásáról vagy egy meglévő szerkesztéséről: a forgatókönyvek [használata az Azure sentinelben](tutorial-respond-threats-playbook.md).



## <a name="next-steps"></a>További lépések
Ebből az oktatóanyagból megtudhatta, hogyan kezdheti el a fenyegetések észlelését az Azure Sentinel használatával. 

Ha szeretné megtudni, hogyan automatizálhatja a fenyegetésekre adott válaszokat, [Hogyan reagálhat a fenyegetésekre automatizált forgatókönyvek használatával](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Válaszoljon](tutorial-respond-threats-playbook.md) a fenyegetésekre, hogy automatizálja a fenyegetésekre adott válaszokat.

