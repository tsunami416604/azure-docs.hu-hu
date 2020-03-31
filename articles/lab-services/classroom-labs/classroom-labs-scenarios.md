---
title: Tantermi laborok használata edzésekhez – Azure Lab Services
description: Ez a cikk ismerteti, hogyan használhatja az Azure DevTest Labs-t az Azure-on a képzési forgatókönyvekhez létrehozott laborok létrehozásához.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717983"
---
# <a name="use-classroom-labs-for-trainings"></a>Tantermi laborok használata tréningekhez
Beállíthat egy labort a képzésekhez. Az Azure Lab Services tantermi laborjai lehetővé teszik, hogy hozzon létre egy labort a képzéshez, ahol minden gyakornok azonos és elszigetelt környezeteket használ a képzéshez. Szabályzatokat alkalmazhat annak biztosítására, hogy a képzési környezetek csak akkor legyenek elérhetők minden gyakornok számára, amikor szükségük van rájuk, és elegendő erőforrást – például virtuális gépeket – tartalmaznak a képzéshez. 

![Tantermi labor](../media/classroom-labs-scenarios/classroom.png)

A Tantermi labs az alábbi követelményeknek felel meg, amelyek a képzés elvégzéséhez szükségesek bármilyen virtuális környezetben: 

- A gyakornokok gyorsan elláthatják képzési környezetüket
- Minden edzőgépnek azonosnak kell lennie
- A gyakornokok nem láthatják a más gyakornokok által létrehozott virtuális gépeket
- A költségek szabályozása annak biztosításával, hogy a gyakornokok ne kaphassanak több virtuális gépet, mint amennyi a képzéshez szükséges, valamint a virtuális gépek leállítása, amikor nem használják őket
- Egyszerűen megoszthatja a képzési labort minden gyakornokkal
- Használja újra és újra az edzéslabort

Ebben a cikkben megismerheti a különböző Azure Lab Services-funkciók, amelyek a korábban leírt képzési követelmények és a részletes lépéseket, amelyek segítségével egy labor képzési beállítása.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>A tesztkörnyezet-fiók létrehozása tesztkörnyezet-fiók-rendszergazdaként
Az Azure Lab Services használatának első lépése egy laborfiók létrehozása az Azure Portalon. Miután egy tesztkörnyezet-fiók rendszergazdája létrehozza a tesztkörnyezet-fiókot, a rendszergazda hozzáadja azokat a felhasználókat, akik laborokat szeretnének létrehozni a **Lab Creator** szerepkörhöz. Az oktatók létre laborok virtuális gépek a diákok számára, hogy nem gyakorlatok at a tanfolyam tanítják. További információt a [Laborfiók létrehozása és kezelése című témakörben talál.](how-to-manage-lab-accounts.md)

## <a name="create-and-manage-classroom-labs"></a>Osztálytermi tesztkörnyezetek létrehozása és kezelése
Egy oktató, aki tagja a Lab Creator szerepkör egy laborfiókban, létrehozhat egy vagy több labs a laborfiókban. Hozzon létre és konfiguráljon egy sablonvirtuális gép az összes szükséges szoftvert a gyakorlatok a tanfolyamon. Válasszon egy kész képet a rendelkezésre álló képek ettantermi labor létrehozásához, majd testre szabhatja a laborhoz szükséges szoftver telepítésével. További információt az [Osztálytermi laborok létrehozása és kezelése című témakörben talál.](how-to-manage-classroom-labs.md)

## <a name="configure-usage-settings-and-policies"></a>Használati beállítások és házirendek konfigurálása
A tesztkörnyezet létrehozója felhasználókat adhat hozzá vagy távolíthat el a laborba, regisztrációs hivatkozást kaphat a tesztkörnyezet felhasználóinak, házirendeket állíthat be, például beállíthatja az egyes kvótákat felhasználónként, frissítheti a tesztkörnyezetben elérhető virtuális gépek számát stb. További információt a [Használati beállítások és házirendek konfigurálása című témakörben talál.](how-to-configure-student-usage.md)

## <a name="create-and-manage-schedules"></a>Ütemezések létrehozása és felügyelete
Az ütemezések lehetővé teszik egy tantermi labor konfigurálását úgy, hogy a tesztkörnyezetben lévő virtuális gépek automatikusan elinduljanak és leállítsanak egy adott időpontban. Megadhatja az egyszeri vagy az ismétlődő ütemezést. További információt az [Osztálytermi laborok ütemezésének létrehozása és kezelése című témakörben talál.](how-to-create-schedules.md)

## <a name="set-up-and-publish-a-template-vm"></a>Sablon beállítása és közzététele virtuális gép
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Állítsa be a sablon virtuális gép, hogy pontosan mit szeretne biztosítani a képzés résztvevői. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. Ezután tegye közzé a sablont, hogy a sablon virtuális gép példányai elérhetővé tegyék a tesztkörnyezet felhasználói számára. Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval. További információt a [Sablon virtuális gépek beállítása és közzététele.](how-to-create-manage-template.md) 

## <a name="use-vms-in-the-classroom-lab"></a>Virtuális gépek használata az osztályteremben laborban
Egy diák vagy képzési résztvevő regisztrál a laborba, és csatlakozik a virtuális géphez, hogy gyakorlatokat végezzen a tanfolyamhoz. További információt a [Tantermi labor elérése.](how-to-use-classroom-lab.md)

## <a name="next-steps"></a>További lépések
Kezdje azzal, hogy laborfiókot hoz létre a Tantermi labsben a cikkben található utasítások szerint: [Oktatóanyag: Laborfiók beállítása az Azure Lab Services szolgáltatással.](tutorial-setup-lab-account.md)