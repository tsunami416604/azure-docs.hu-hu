---
title: Feladatátvétel az Azure hibák elhárítása |} Microsoft Docs
description: A cikk ismerteti az Azure-bA feladatátvételét gyakori hibáinak elhárítása
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 5c94e26c4639284f7e4c53d924f16040118d996c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874359"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Ha az Azure virtuális gép feladatátvétele hibák elhárítása
Hibák a következők egyike jelenhet meg közben a virtuális gép az Azure-bA. Hibaelhárítás, az ismertetett lépésekkel az egyes.


## <a name="failover-failed-with-error-id-28031"></a>Hibaazonosító 28031 feladatátvétele sikertelen volt

A Site Recovery nem tudott létrehozni egy sikertelen átadó virtuális gép az Azure-ban. Ez a következő okok valamelyike miatt fordulhat elő:

* Nem áll rendelkezésre a virtuális gép létrehozása kvóta elegendő: előfizetés megnyitásával ellenőrizheti a rendelkezésre álló kvóta -> használati + kvótákat. Megnyithatja a [új támogatási kérelem](http://aka.ms/getazuresupport) a kvóta növeléséhez.

* Különböző méretű kártevőcsaládok azonos rendelkezésre állási csoport virtuális gépeinek feladatátvételi próbál. Gondoskodjon arról, hogy az összes virtuális gép ugyanazon mérete család választja az azonos rendelkezésre állási készlet. Az oldalméret módosítása a virtuális gép számítási és hálózati beállításainak megnyitásával, majd próbálkozzon újra a feladatátvételt.

* Az előfizetés, amely megakadályozza a virtuális gép létrehozásához a házirend nincs. Módosítsa a házirendet, engedélyezi a virtuális gépek létrehozását, majd próbálkozzon újra a feladatátvételt.

## <a name="failover-failed-with-error-id-28092"></a>Hibaazonosító 28092 feladatátvétele sikertelen volt

A Site Recovery nem tudta hozzon létre egy hálózati összeköttetést a feladatokat átadó virtuális gép. Győződjön meg arról, hogy a hálózati adapterek létrehozása az előfizetésben elérhető kvóta elegendő. Előfizetés megnyitásával ellenőrizheti a rendelkezésre álló kvóta -> használati + kvótákat. Megnyithatja a [új támogatási kérelem](http://aka.ms/getazuresupport) a kvóta növeléséhez. Ha rendelkezik elegendő kvótával, akkor ez egy időszakos lehet mellett próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, még akkor is ismételt próbálkozások után sem, ezután a helyén hagyja a dokumentum végén megjegyzést.  

## <a name="failover-failed-with-error-id-70038"></a>Hibaazonosító 70038 feladatátvétele sikertelen volt

A Site Recovery nem tudott létrehozni egy sikertelen keresztül az Azure klasszikus virtuális géphez. Ez akkor fordulhat elő, mert:

* Például egy virtuális hálózatot a virtuális gép létrehozásához szükséges erőforrások egyike nem létezik. A virtuális hálózat létrehozása a virtuális gép számítási és hálózati beállításainak előírt, vagy módosítsa a beállítást, amely már létezik, és próbálkozzon újra a feladatátvételi virtuális hálózathoz.


## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, majd tegye fel a lekérdezés [Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) vagy ez a dokumentum végén megjegyzés. Van egy aktív Közösség, amely segít képesnek kell lennie.
