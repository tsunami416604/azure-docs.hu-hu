---
title: A feladatok ütemezése
description: A feladatok kezeléséhez használja a feladatütemezést.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672432"
---
# <a name="schedule-jobs-for-efficiency"></a>Feladatok ütemezése a hatékonyság érdekében

A Kötegelt feladatok ütemezése lehetővé teszi, hogy rangsorolja azokat a feladatokat, amelyeket először futtatni szeretne, miközben figyelembe veszi azokat a feladatokat, amelyek más tevékenységektől függenek. A feladatok ütemezésével biztosíthatja, hogy a legkevesebb erőforrást használja. A csomópontok akkor szerelhetők le, ha nincs rá szükség, a más feladatoktól függő feladatok éppen időben, a munkafolyamatok optimalizálásával jelennek meg. Egyszerre csak egy feladat fut. Egy új nem indul el, amíg az előző be nem fejeződik. Beállíthatja, hogy a feladat automatikus kiegészítés. 

## <a name="benefit-of-job-scheduling"></a>A feladatütemezés előnyei

A feladatok ütemezésének előnye, hogy megadhat egy ütemtervet a munkahelyteremtéshez. A feladatkezelő feladattal ütemezett feladatok egy feladathoz vannak társítva. A feladatkezelő feladat feladatokat hoz létre a feladathoz. Ehhez a feladatkezelő feladatnak hitelesítenie kell magát a Batch-fiókkal. Használja a AZ_BATCH_AUTHENTICATION_TOKEN hozzáférési jogkivonatot. A jogkivonat hozzáférést biztosít a feladat többi részéhez. 

## <a name="use-the-portal-to-schedule-a-job"></a>Feladat ütemezése a portálhasználatával

   1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

   2. Válassza ki azt a Batch-fiókot, amelybe feladatokat szeretne ütemezni.

   3. Új feladatütemezés létrehozásához és az **Alapképernyő kitöltéséhez**válassza a **Hozzáadás** lehetőséget.



![Feladat ütemezése][1]

**Munkaütemezés-azonosító**: A feladatütemezés egyedi azonosítója.

**Megjelenítendő név**: A feladat megjelenítendő nevének nem kell egyedinek lennie, de legfeljebb 1024 karakter hosszúságúnak kell lennie.

**Ne fusson, amíg**: Megadja a feladat futtatásának legkorábbi idejét. Ha ezt nem állítja be, az ütemezés azonnal készen áll a feladatok futtatására.

**Ne fusson a következő után:** Nincs feladat fut az itt beállított idő után. Ha nem ad meg időpontot, akkor ismétlődő feladatütemezést hoz létre, amely addig marad aktív, amíg kifejezetten le nem állítja.

**Ismétlődési időköz**: Megadhatja a feladatok közötti időt. Egyszerre csak egy feladat lehet ütemezve, így ha itt az ideje, hogy új feladatot hozzon létre egy feladatütemezés alatt, de az előző feladat még mindig fut, a Batch szolgáltatás nem hozza létre az új feladatot, amíg az előző feladat be nem fejeződik.  

**Kezdőablak**: Itt adhatja meg az időintervallumot, amely attól az időponttól kezdődik, amikor az ütemezés azt jelzi, hogy egy feladatot létre kell hozni, amíg be nem fejeződik. Ha az aktuális feladat nem fejeződik be az ablak alatt, a következő feladat nem indul el.

Az alapűrlap alján adhatja meg azt a készletet, amelyen a feladatot futtatni szeretné. A készletazonosító adatainak megkereséséhez válassza a **Frissítés lehetőséget.** 

![Készlet megadása][2]


**Készlet azonosító:** azonosítsa a készlet fog futni a feladatot.

**Feladatkonfigurációs feladat**: Válassza a **Frissítés** lehetőséget a Feladatkezelő feladat, valamint a Feladat-előkészítési és kiadási feladatok elnevezéséhez, ha használja őket.

**Prioritás**: Adjon elsőbbséget a feladatnak.

**Maximális falióra-idő:** Állítsa be azt a maximális időtartamot, amerre a feladat futhat. Ha nem fejeződik be az időkereten belül, batch leállítja a feladatot. Ha ezt nem állítja be, akkor nincs határidő a feladatra.

**Feladat újrapróbálkozások maximális száma**: Adja meg, hogy egy feladat hányszor próbálható újra legfeljebb négy alkalommal. Ez nem egyezik meg az API-hívás újrapróbálkozásainak számával.

**Ha az összes feladat befejeződött:** Az alapértelmezett beállítás nem művelet.

**Ha egy feladat sikertelen**: Az alapértelmezett beállítás nem művelet. Egy feladat sikertelen, ha az újrapróbálkozások száma kimerült, vagy hiba történt a feladat indításakor. 

Miután a **Mentés**lehetőséget választotta, ha a bal oldali navigációs sávban a **Feladatütemezések lehetőséget választja,** a **Végrehajtási adatok**lehetőség kiválasztásával nyomon követheti a feladat végrehajtását.


## <a name="for-more-information"></a>További tudnivalók

Ha az Azure CLI használatával szeretne kezelni egy feladatot, olvassa [el az az kötegelt feladat ütemezése című témakört.](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest)

## <a name="next-steps"></a>További lépések

[Tevékenységfüggőségek létrehozása más feladatoktól függő feladatok futtatásához.](batch-task-dependencies.md)





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


