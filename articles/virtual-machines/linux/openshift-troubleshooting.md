---
title: "Az Azure-ban a OpenShift üzembe helyezés hibaelhárítása |} Microsoft Docs"
description: "Az Azure-ban a OpenShift üzembe helyezés hibaelhárítása"
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
ms.openlocfilehash: ea3664870480f6ed170972a5f52940dc4a852219
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshooting-openshift-deployment-in-azure"></a>Az Azure-ban a OpenShift üzembe helyezés hibaelhárítása

Ha a OpenShift fürt telepítése nem sikerült, van néhány hibaelhárítási feladatokat végezheti el a problémát szűkítéséhez. A központi telepítés állapotának megtekintése, és hasonlítsa össze a következő kilépési kódot listája alapján.

- 3. kilépési kód: A Red Hat előfizetés felhasználónév / jelszó vagy a szervezet azonosítója / aktiválási kulcs érvénytelen
- 4. kilépési kód: A Red Hat alkalmazáskészlet azonosítója érvénytelen, vagy nincsenek elérhető nincs jogosultságok
- 5. kilépési kód: Nem sikerült kiépíteni a Docker vékony Tárolókészletének kötetén
- 6. kilépési kód: OpenShift fürt telepítése nem sikerült
- 7. kilépési kód: OpenShift fürt telepítése sikeres volt, de Azure felhő szolgáltató konfigurálása nem sikerült – a fő csomópont probléma fő konfigurációs
- 8. kilépési kód: OpenShift fürt telepítése sikeres volt, de az Azure felhőalapú szolgáltató konfigurálása nem sikerült – Master csomópont probléma csomópont-konfiguráció
- 9. kilépési kód: OpenShift fürt telepítése sikeres volt, de az Azure felhőalapú szolgáltató konfigurálása nem sikerült – Infra vagy alkalmazás csomópontjához probléma csomópont-konfiguráció
- 10. kilépési kód: OpenShift fürt telepítése sikeresen befejeződött, de nem sikerült – a fő csomópontok javítása Azure Cloud szolgáltató konfigurációja vagy nem állíthatja be a fő szerint unschedulable
- 11. kilépési kód: Sikertelen volt a központi telepítése
- 12. kilépési kód: Naplózási rendszerbe állítása sikertelen volt

A kilépési kódot 7 – 10. a OpenShift fürt telepítése volt, de az Azure felhőalapú szolgáltató konfigurálása nem sikerült. A fő (forrás) vagy megerősített csomópontot (tároló Platform) és az ott SSH a fürtben lévő csomópontok mindegyikének SSH is, és hárítsa el a problémákat.

Az oka leggyakrabban a hibák, a 7. kilépési kód – 9, a szolgáltatás egyszerű nem rendelkezett a megfelelő engedélyekkel az előfizetés vagy az erőforráscsoport. Ha valóban ez a probléma, majd rendelje hozzá a megfelelő engedélyekkel, és manuálisan futtassa újra a parancsfájl az összes későbbi parancsfájl sikertelen.
Győződjön meg arról, hogy indítsa újra a szolgáltatást (például systemctl újraindítás atomi-openshift-node.service) előtt a parancsfájlok végrehajtása ismét sikertelen.

Az SSH-ból a fő csomópont port 2200 (forrás) és a megerősített csomópontot a 22-es portot (tároló Platform) hibaelhárításáról további. Kell lennie a gyökér (sudo su-), és keresse meg a következő könyvtár: /var/lib/waagent/custom-script/download

Meg kell jelennie egy "0" és "1" nevű mappát. Az egyes ezeket a mappákat lásd a két fájlt, a stderr-en és a stdout. Ezeket a fájlokat, annak meghatározásához, ahol a hiba fellépett keresztül tekintheti meg.
