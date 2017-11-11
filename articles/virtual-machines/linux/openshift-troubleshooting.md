---
title: "Az Azure-ban OpenShift telepítési hibáinak elhárítása |} Microsoft Docs"
description: "Végezzen hibaelhárítást a OpenShift telepítése az Azure-ban."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Az Azure-ban OpenShift telepítési hibáinak elhárítása

Ha a OpenShift fürt telepítése nem sikerült, próbálja meg ezek a hibaelhárítási feladatok szűkítéséhez a problémát. A központi telepítés állapotának megtekintése és összevetni a kilépési kódot a következők közül:

- Kilépési kód 3: A Red Hat előfizetés felhasználónév / jelszó vagy a szervezet azonosítója / aktiválási kulcs érvénytelen.
- Kilépési kód 4: A Red Hat alkalmazáskészlet azonosítója érvénytelen, vagy nincsenek elérhető nincs jogosultságok
- Kilépési kód 5: nem sikerült kiépíteni a Docker vékony Tárolókészletének kötetén
- Kilépési kód 6: OpenShift fürt telepítése nem sikerült
- Kilépési kód 7: OpenShift fürt telepítése sikeres volt, de nem sikerült – Azure Cloud Solution Provider konfigurációs fő config fő csomópont probléma
- Kilépési kód 8: OpenShift fürt telepítése sikeres volt, de nem sikerült – Azure Cloud Solution Provider konfigurációs fő csomópont probléma csomópont-konfiguráció
- Kilépési kód 9: OpenShift fürt telepítése sikeres volt, de nem sikerült – Azure Cloud Solution Provider konfigurációs Infra vagy alkalmazás csomópontjához probléma csomópont-konfiguráció
- Kilépési kód 10: OpenShift fürt telepítése sikeresen befejeződött, de nem sikerült – a fő csomópontok javítása Azure Cloud Solution Provider konfigurációja vagy nem állíthatja be a fő szerint unschedulable
- Kilépési kód 11: sikertelen volt a központi telepítése
- Kilépési kód 12: naplózási rendszerbe állítása sikertelen volt

A kilépési kódot 7 – 10. a OpenShift fürt telepítése, de az Azure Cloud Solution Provider konfigurálása nem sikerült. A fő (OpenShift származási) vagy a megerősített csomópontot (OpenShift tároló Platform), és az ott SSH fürt minden csomópontja számára a problémák megoldásával SSH is.

Egy gyakori kilépési kódot 7 – 9 rendelkező hibák oka, hogy a szolgáltatás egyszerű nem rendelkezett megfelelő engedélyekkel az előfizetés vagy az erőforráscsoport. Ha ez a probléma, rendelje hozzá a megfelelő engedélyekkel, és manuálisan futtassa újra a parancsfájlban, melyeknél nem sikerült, és minden további parancsfájlok.

Győződjön meg arról, hogy indítsa újra a szolgáltatást (például systemctl újraindítás atomi-openshift-node.service) előtt a parancsfájlok végrehajtása ismét sikertelen.

A további a hibaelhárítás SSH-ból a fő port 2200 (forrás) vagy a megerősített csomópontot (tároló Platform) 22-es porton. A legfelső szintű (sudo su-), és tallózással keressen meg az alábbi könyvtárból kell: /var/lib/waagent/custom-script/download.

Itt láthatja a mappák nevű, "0" és "1". Minden egyes ezeket a mappákat látható két, "stderr" és "stdout." Nézze át ezeket a fájlokat, annak meghatározásához, ahol a hiba fellépett.
