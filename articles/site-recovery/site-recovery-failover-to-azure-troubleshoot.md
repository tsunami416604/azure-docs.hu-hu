---
title: Hibák az Azure-ba irányuló feladatátvétel hibaelhárítása |} A Microsoft Docs
description: Ez a cikk ismerteti azokat a módszereket, az Azure-ba irányuló feladatátvétel előforduló gyakori hibák elhárítása
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
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: b7b5dcd88b6e4e09dd9beb21e83ef405df148115
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443384"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Az Azure virtuális gép feladatátvétele során előforduló hibák elhárítása

Az Azure-bA egy virtuális gép feladatátvétele közben hibák a következők egyike jelenhet meg. A hiba elhárításához használja az itt ismertetett lépéseket az egyes hibaállapotokhoz.

## <a name="failover-failed-with-error-id-28031"></a>Hibaazonosító: 28031 feladatátvétel sikertelen

A Site Recovery nem tudta egy nem sikerült létrehozni a feladatátviteli virtuális géphez az Azure-ban. Ez a következő okok egyike miatt fordulhat elő:

* Nem áll rendelkezésre elegendő mag érhető el a virtuális gép létrehozásához: a rendelkezésre álló kvótát ellenőrizheti a előfizetés -> használat + kvóták. Megnyithatja a [új támogatási kérelem](http://aka.ms/getazuresupport) a kvóta növeléséhez.

* A feladatátvételi virtuális gépek ugyanazon rendelkezésre állási csoportban lévő különböző méretcsaládokhoz szeretne. Győződjön meg arról, hogy ugyanabban a rendelkezésre állási csoportban válassza ki az összes virtuális gép ugyanazon virtuálisgépméret-családhoz. Nyissa meg a virtuális gép számítási és hálózati beállításainál módosíthatja a méretét, és próbálkozzon újra a feladatátvételt.

* Nincs szabályzat az előfizetést, amely megakadályozza a virtuális gép létrehozását. Módosítsa a házirendet, engedélyezi a virtuális gépet, majd próbálkozzon újra a feladatátvételt.

## <a name="failover-failed-with-error-id-28092"></a>Hibaazonosító: 28092 feladatátvétel sikertelen

A Site Recovery nem tudta hozzon létre egy hálózati adaptert a sikertelen a feladatátviteli virtuális géphez. Győződjön meg arról, hogy elegendő kvótával a hálózati adapterek létrehozása az előfizetésben elérhető. A rendelkezésre álló kvótát ellenőrizheti a előfizetés -> használat + kvóták. Megnyithatja a [új támogatási kérelem](http://aka.ms/getazuresupport) a kvóta növeléséhez. Ha elegendő kvótával rendelkezik, akkor ez lehet egy átmeneti probléma, próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, ismétlés után sem, majd a dokumentum végén található Megjegyzés írása.  

## <a name="failover-failed-with-error-id-70038"></a>Feladatátvétel sikertelen Hibaazonosító: 70038

A Site Recovery nem tudta egy nem sikerült létrehozni a klasszikus virtuális gép az Azure-ban keresztül. Ez akkor fordulhat elő, mert:

* Például egy virtuális hálózatot a virtuális gépet létrehozni a szükséges erőforrások egyike nem létezik. A virtuális hálózat létrehozása a virtuális gép számítási és hálózati beállításaiban meg vagy módosítsa a beállítást, amely már létezik, és próbálkozzon újra a feladatátvételi virtuális hálózathoz.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nem lehet csatlakozni RDP/SSH a feladatátviteli virtuális géphez oka az, hogy szürkén jelenik meg a virtuális gépen a csatlakozás gomb

Ha a csatlakozás gomb szürkén jelenik meg, és az Express Route vagy helyek közötti VPN-kapcsolaton keresztül, ezt követően nem csatlakozik Azure

1. Lépjen a **virtuális gép** > **hálózatkezelés**, kattintson a szükséges hálózati kapcsolat neve.  ![hálózati-adapter](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
1. Navigáljon a **Ip-konfigurációk**, majd kattintson a szükséges IP-konfiguráció a név mező. ![IP-konfigurációk](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
1. Ahhoz, hogy a nyilvános IP-címet, kattintson a **engedélyezése**. ![IP engedélyezése](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
1. Kattintson a **kötelező beállítások konfigurálása** > **új létrehozása**. ![Új létrehozása](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
1. Adja meg a nyilvános cím nevére, és válassza ki az alapértelmezett beállításainak **Termékváltozat** és **hozzárendelés**, majd kattintson a **OK**.
1. Most a módosítások mentéséhez kattintson **mentése**.
1. Zárja be a panelt, és keresse meg a **áttekintése** virtuális gép csatlakoztatása/RDP szakaszában.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Nem lehet csatlakozni RDP/SSH a keresztül a virtuális gép akkor is, ha a csatlakozás gomb érhető el (szürkén nem jelenik meg) a virtuális gépen

Ellenőrizze **rendszerindítási diagnosztika** a virtuális gép és ellenőrzése ebben a cikkben felsorolt hibákat.

1. Ha a virtuális gép nem indult el, próbálja meg átvitelét egy régebbi helyreállítási pontra.
1. Ha az alkalmazás a virtuális gépen belül nem átvitelét, próbálkozzon egy alkalmazáskonzisztens helyreállítási pontra.
1. Ha a virtuális gép tartományhoz, majd győződjön meg arról, hogy a tartományvezérlő pontosan működik. A következő ehhez az alábbiakban megadott lépéseket.
    a. Hozzon létre egy új virtuális gép ugyanazon a hálózaton

    b.  Győződjön meg arról, hogy csatlakozni tudjon ugyanahhoz a tartományhoz, amely a feladatátviteli virtuális géphez várhatóan merülnek fel.

    c. Ha a tartományvezérlő **nem** működik pontosan, majd próbálja jelentkezzen be a feladatátviteli virtuális gépre a helyi rendszergazdai fiókkal
1. Ha egyéni DNS-kiszolgálót használ, majd győződjön meg arról, hogy legyen elérhető. A következő ehhez az alábbiakban megadott lépéseket.
    a. Hozzon létre egy új virtuális gép a hálózat és a b. Ellenőrizze, hogy a virtuális gép tud-e névfeloldást végezni az egyéni DNS-kiszolgáló használatával

>[!Note]
>Azure Virtuálisgép-ügynököt kell telepíteni a virtuális gépet a feladatátvétel előtt a rendszerindítási Diagnosztikán kívül más beállítások engedélyezéséhez lenne szükség

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, majd el a lekérdezést az [Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) vagy ez a dokumentum végén található Megjegyzés írása. Van egy aktív Közösség, amely segítségére képesnek kell lennie.
