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
ms.openlocfilehash: 838eac510fc17d56f808f541f4e205a279f63c56
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318891"
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

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nem lehet csatlakozni vagy RDP/SSH a feladatokat átadó virtuális gép, mert a szürkén jelenik meg a Csatlakozás gombra a virtuális gépen

Ha a csatlakozás gomb szürkén jelenik meg, és az Express Route vagy telephelyek közötti VPN-kapcsolaton keresztül, majd, nem csatlakozik Azure

1. Ugrás a **virtuális gép** > **hálózati**, kattintson a szükséges hálózati kapcsolat neve.  ![hálózati-illesztő](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navigáljon a **IP-konfigurációk**, majd kattintson az a név mező szükséges IP-konfiguráció. ![IP-konfigurációk](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Ahhoz, hogy a nyilvános IP-cím, kattintson a **engedélyezése**. ![IP-címek](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Kattintson a **kötelező beállítások konfigurálása** > **hozzon létre új**. ![Új létrehozása](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Adja meg a nevét, nyilvános cím, az alapértelmezett beállítások megadása **SKU** és **hozzárendelés**, majd kattintson a **OK**.
6. Most, a módosítások mentéséhez kattintson **mentése**.
7. Zárja be a panelt, és keresse meg **áttekintése**  /RDP csatlakozzon a virtuális gép szakasza.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Nem lehet csatlakozni vagy RDP/SSH-kapcsolatot a sikertelen keresztül a virtuális gép akkor is, ha a Csatlakozás gombra érhető el (szürkén nem jelenik meg) a virtuális gépen

Ellenőrizze **rendszerindítási diagnosztika** a virtuális gépen, a cikkben felsorolt hibák.

1. Ha a virtuális gép nem indult el, próbálkozzon egy korábbi helyreállítási pontra feladatátvétele.
2. Ha az alkalmazás a virtuális gépen nem működik-e az alkalmazáskonzisztens helyreállítási pontnak feladatátvételét próbálja meg.
3. Ha a virtuális gép tartományhoz csatlakozik, majd győződjön meg arról, hogy a tartományvezérlő működik pontosan. Következő ehhez az alább megadott lépéseket.
    a. Hozzon létre egy új virtuális gép ugyanazon a hálózaton

    b.  Győződjön meg arról, hogy az ugyanahhoz a tartományhoz, amelyen csatlakozhat a feladatokat átadó virtuális gép várható elérni.

    c. Ha a tartományvezérlő **nem** működik pontosan, ismételje meg a bejelentkezést a feladatokat átadó virtuális gép egy helyi rendszergazdai fiók használatával
4. Ha egy egyéni DNS-kiszolgálót használ, majd győződjön meg arról, hogy az elérhető-e. Következő ehhez az alább megadott lépéseket.
    a. Hozzon létre egy új virtuális gép a hálózat és a b. Ellenőrizze, hogy a virtuális gép tudni az egyéni DNS-kiszolgáló segítségével névfeloldás

>[!Note]
>Rendszerindítási diagnosztika kívül bármely beállítást igényel Azure VM ügynököt kell telepíteni a virtuális gépről a feladatátvétel előtt

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, majd tegye fel a lekérdezés [Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) vagy ez a dokumentum végén megjegyzés. Van egy aktív Közösség, amely segít képesnek kell lennie.
