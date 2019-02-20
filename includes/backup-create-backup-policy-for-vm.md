---
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 11/09/2018
ms.author: raynew
ms.openlocfilehash: b589c88e5b5c5991db43a9f3c10003e17094b2e1
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430870"
---
## <a name="defining-a-backup-policy"></a>Biztonsági mentési házirend meghatározása
A biztonsági mentési házirend egy mátrixot határoz meg ahhoz, hogy mikor készüljön pillanatkép az adatokról, és mennyi ideig őrizze meg a rendszer a pillanatképeket. Virtuális gépek biztonsági mentéséről szóló házirend meghatározása esetén *naponta egyszer* indíthat el biztonsági mentési feladatot. Az újonnan létrehozott házirendek a tárolón érvényesülnek. A biztonsági mentési házirendek felülete a következőképpen néz ki:

![Biztonsági mentési házirend](./media/backup-create-policy-for-vms/backup-policy.png)

Házirend létrehozása:

1. Adjon meg egy nevet a **Házirend neve** mezőben.
2. Az adatokról napi vagy heti rendszerességgel készíthet pillanatképet. A **Biztonsági mentés gyakorisága** legördülő menüben kiválaszthatja, hogy napi vagy heti rendszerességgel szeretne pillanatképet készíteni az adatokról.

   * Ha a napi rendszerességet választja, a kiemelt vezérlővel kiválaszthatja, hogy a nap melyik szakában szeretné elkészíteni a pillanatképet. Ha módosítani szeretné a kiválasztott órát, törölje a kijelölését, majd válasszon ki egy másikat.

     ![Napi biztonsági mentési házirend](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>
   * Ha a heti rendszerességet választja, a kiemelt vezérlőkkel kiválaszthatja, hogy a hét melyik napjain, illetve a nap melyik szakában szeretné elkészíteni a pillanatképet. A nap menüben válasszon ki egy vagy több napot. Az óra menüben válasszon ki egy órát. Ha módosítani szeretné a kiválasztott órát, törölje a kijelölését, majd válasszon ki egy másikat.

     ![Heti biztonsági mentési házirend](./media/backup-create-policy-for-vms/backup-policy-weekly.png)
3. Alapértelmezés szerint az összes **Megőrzési időtartam** beállítás ki van választva. Törölje a megőrzési időtartam azon korlátozásainak kijelölését, amelyeket nem szeretne használni. Ezután adja meg használni kívánt időtartamo(ka)t.

    A havi és éves megőrzési időtartamok lehetővé teszik a heti vagy napi növekményes pillanatképek megadását.

   > [!NOTE]
   >
  - A virtuális gépek védelméhez a rendszer naponta egyszer készít biztonsági mentést. A biztonsági mentés futtatásának ideje minden megőrzési időtartam esetén ugyanaz.
  - A dátum és idő, amikor a biztonsági mentés pillanatképének befejeződött függetlenül attól, amikor a biztonsági mentési feladat ütemezés volt a helyreállítási pont jön létre.
    - Pl. Ha a biztonsági mentés gyakorisága: 11:30 = 1997031213 van ütemezve, és bármilyen probléma miatt pillanatkép befejeződött, 12:01-kor, majd a helyreállítási pont jön a dátumot és 12:01-kor.
  - Havi biztonsági mentés esetén ha a biztonsági mentés futtatása minden hónap első napján van beállítva, és a pillanatkép befejezése néhány probléma miatt a következő napon majd a helyreállítási pont létrehozása a havonkénti biztonsági mentés címkével fog rendelkezni a következő napra (vagyis) második az adott hónap).
   >
   >


4. A házirend beállításainak megadása után kattintson a panel tetején található **Mentés** gombra.

    Ekkor a rendszer automatikusan alkalmazza az új házirendet a tárolón.
