---
title: "Célcsoport-kezelési megoldások Azure-ban |} Microsoft Docs"
description: "Célcsoport-kezelési megoldások lehetővé teszi az ügynökök meghatározott megoldások korlátozza.  Ez a cikk ismerteti, hogyan segítségével hozzon létre egy hatókör konfigurációját, és a megoldáshoz."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: e24a7540a67f842b8229223bf6602cd9e0ee5dbd
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="targeting-management-solutions-in-azure-preview"></a>Az Azure (előzetes verzió) célcsoport-kezelési megoldások
Ha olyan felügyeleti megoldást hozzáadása az előfizetéshez, automatikusan telepíti a Naplóelemzési munkaterület csatlakozó Windows és Linux ügynökök alapértelmezés szerint.  Érdemes lehet a költségek kezelésére, és egy meghatározott ügynökök korlátozásával megoldás gyűjtött adatok mennyiségét.  Ez a cikk ismerteti, hogyan használható **célcsoport-kezelési megoldás** Ez lehetővé teszi, hogy a hatókör alkalmazhat a megoldásokat.

## <a name="how-to-target-a-solution"></a>A megoldás bemutatásához
A következő szakaszokban ismertetett módon célcsoport-kezelési megoldás három lépésben történik. 


### <a name="1-create-a-computer-group"></a>1. Hozzon létre egy számítógépcsoportot
A számítógépek, hozzon létre egy hatókört szerepeltetni kívánt megadása egy [számítógépcsoport](../log-analytics/log-analytics-computer-groups.md) a Naplóelemzési.  A számítógép csoport naplófájl-keresési alapján, vagy más forrásokból, például az Active Directory vagy a WSUS-csoportok importálása. Mint [az alábbiakban](#solutions-and-agents-that-cant-be-targeted), csak a Naplóelemzési közvetlenül csatlakozó számítógépek megtalálható a hatókörben.

Követően létre a munkaterületén számítógépcsoport akkor kell szerepeltetni a hatókör konfigurációját, amelyek egy vagy több megoldásokat alkalmazhatja.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. Hatókör-konfiguráció létrehozása
 A **hatókör konfigurációjának** egy vagy több számítógép csoportot tartalmaz, és egy vagy több megoldásokat alkalmazhatja. 
 
 A következő eljárással hatókör-konfiguráció létrehozása.  

 1. Az Azure-portálon lépjen a **Naplóelemzési** válassza ki a munkaterületen.
 2. A munkaterület tulajdonságainak **munkaterület adatforrások** válasszon **hatókör konfigurációk**.
 3. Kattintson a **Hozzáadás** hatókör új konfiguráció létrehozásához.
 4. Adjon meg egy **neve** hatókör-konfigurációhoz.
 5. Kattintson a **válassza ki a számítógépcsoportokat**.
 6. Válassza ki a létrehozott számítógépcsoport és a nem kötelezően bármely egyéb csoportok hozzáadni a konfigurációhoz.  Kattintson a **Kiválasztás** gombra.  
 6. Kattintson a **OK** létrehozása a hatókör beállításait. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. A hatókör konfigurációjának alkalmazása megoldásokhoz.
Miután a hatókör konfigurációját, majd alkalmazhatja azt egy vagy több megoldásokhoz.  Vegye figyelembe, hogy egy egyetlen hatókör konfigurációjának több megoldás is használható, amíg minden megoldás csak használhatja egy hatókör-konfigurációt.

A hatókör konfigurációját, a következő eljárással alkalmazni.  

 1. Az Azure-portálon lépjen a **Naplóelemzési** válassza ki a munkaterületen.
 2. Válassza ki a munkaterület tulajdonságainak **megoldások**.
 3. Kattintson a kívánt hatókör-megoldásban.
 4. A megoldás tulajdonságainak **munkaterület adatforrások** kiválasztása **célcsoport-kezelési megoldás**.  Ha a beállítás nem érhető el majd [Ez a megoldás nem tudja megcélozni](#solutions-and-agents-that-cant-be-targeted).
 5. Kattintson a **Hozzáadás hatókör konfigurációjának**.  Ha már rendelkezik egy konfigurációt, akkor ez a beállítás nem érhető el ebben a megoldásban alkalmazott.  A meglévő konfigurációt felvétele előtt el kell távolítania.
 6. Kattintson a hatókör beállításait a létrehozott.
 7. Tekintse meg a **állapot** a konfigurációban, hogy azt mutatja, hogy a **sikeres**.  Ha az állapot hibát jelez, majd kattintson a jobb oldalán a konfigurációt, és válassza a három pont **Szerkesztés hatókör konfigurációjának** módosításokat.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Megoldások és az ügynököket, amelyeket nem telepíthető
Az alábbiakban az ügynök és a megoldások, amelyek nem használható a célcsoport-kezelési megoldás a feltételeket.

- Csak célcsoport-kezelési megoldás által telepítendő ügynökök megoldások vonatkozik.
- Csak célcsoport-kezelési megoldás a Microsoft által biztosított megoldások vonatkozik.  Nem alkalmazható megoldásokhoz [partnerek vagy saját maga által létrehozott](operations-management-suite-solutions-creating.md).
- Ügynököket, amelyeket közvetlen csatlakoztatása a Log Analyticshez csak szűrésére.  Megoldás automatikusan telepíti azokat a hatókör konfigurációját most tartalmazza-e az Operations Manager csatlakoztatott felügyeleti csoport részét képező összes ügynököt.

### <a name="exceptions"></a>Kivételek
Célcsoport-kezelési megoldás nem használható a következő megoldásokat annak ellenére, hogy elférjen a megadott feltételeknek.

- Ügynök állapotfigyelő értékelése

## <a name="next-steps"></a>További lépések
- További információ a megoldások, amelyek telepíthető a környezetben, beleértve a felügyeleti megoldásokra [megoldások Azure Naplóelemzés adja hozzá a munkaterületre](../log-analytics/log-analytics-add-solutions.md).
- További információ a számítógépcsoportok létrehozása [számítógépcsoportokat a Log Analyticshez jelentkezzen keresések](../log-analytics/log-analytics-computer-groups.md).