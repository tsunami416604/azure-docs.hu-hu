---
title: "Azure Functions Runtime telepítésének |} Microsoft Docs"
description: "Az Azure Functions Futtatókörnyezettel telepítése"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: 1e4188313a87d07f396e5f8edc8969dd5da2c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Telepítse az Azure Functions futásidejű előnézete

Ha szeretné telepíteni az Azure Functions Futtatókörnyezettel előzetes, akkor kövesse az alábbi lépéseket:

1. Győződjön meg arról, a gép megfelel a minimális követelményeknek
1. Töltse le a [Azure Functions futásidejű Preview telepítő](https://aka.ms/azafr). 
1. Telepítse az Azure Functions Futtatókörnyezettel előzetes
1. Az Azure Functions Futtatókörnyezettel előzetes konfigurálása

## <a name="prerequisites"></a>Előfeltételek

Az Azure Functions Futtatókörnyezettel előzetes verzió telepítése előtt az alábbiakkal kell rendelkeznie:

1. A Microsoft Windows Server 2016 vagy a Microsoft Windows 10 Creators frissítés (Professional és Enterprise Edition) rendszerű gépek.
1. A hálózaton belül futó SQL Server-példányt.  Minimális edition követelmény az SQL Server Express.

## <a name="install-the-azure-functions-runtime-preview"></a>Telepítse az Azure Functions futásidejű előnézete

Az Azure Functions Futtatókörnyezettel preview telepítő végigvezeti Önt az Azure Functions Futtatókörnyezettel előzetes felügyeleti és feldolgozói szerepkörök telepítését.  Akkor lehet a felügyeleti és a feldolgozói szerepkör telepítése ugyanazon a számítógépen.  Azonban további funkciók hozzáadása, telepítenie kell a további gépek alakzatot több Worker a functions méretezése tennie további feldolgozói szerepkörök is.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>A felügyeleti és a feldolgozói szerepkör telepítése ugyanazon a számítógépen

1. Futtassa az Azure Functions futásidejű Preview telepítőjét.

    ![Az Azure Functions futásidejű Preview telepítő][1]

1. **Kattintson a Tovább gombra** előzetes túli az első szakasza a telepítő
1. Ha mindenképpen olvassa el a használati a **EULA**, **jelölje be a jelölőnégyzetet** a feltételek elfogadásának és **kattintson a Tovább gombra** ahhoz, hogy.
1. Most válassza ki a szerepköröket, ezen a számítógépen telepítendő **funkciók szerepkör** és/vagy **funkciók feldolgozói szerepkör** és **kattintson a Tovább gombra**

    ![Az Azure Functions futásidejű Preview Installer - szerepkör kiválasztása][3]

    > [!NOTE]
    > Telepítheti a **funkciók feldolgozói szerepkör** sok más számítógépeken ehhez, kövesse ezeket az utasításokat, és csak akkor válassza **funkciók feldolgozói szerepkör** a telepítőben.

1. **Kattintson a Tovább gombra** kell rendelkeznie a **Azure Functions futásidejű telepítő** telepítése a számítógépre.
1. Miután befejeződött a telepítő elindítja a **Azure Functions futásidejű konfigurációs eszköz**.

    ![Az Azure Functions futásidejű Preview telepítő befejezése][5]

    > [!NOTE]
    > Ha telepíti az **Windows 10** és a **tároló** funkció nem korábban engedélyezve van, a **Azure Functions Futtatókörnyezettel** előfordulhat, hogy indítsa újra a számítógépet a telepítés befejezéséhez.

## <a name="configure-the-azure-functions-runtime"></a>Az Azure Functions Futtatókörnyezettel konfigurálása

Az Azure Functions Futtatókörnyezettel telepítéséhez a konfigurációt kell végrehajtania.

1. A **Azure Functions futásidejű konfigurációs eszköz** jeleníti meg, mely szerepkörök sincs telepítve a számítógépen.

    ![Az Azure Functions futásidejű Preview konfigurációs eszköz][6]

1. Kattintson a **adatbázis** lapra, adja meg a **kapcsolódási adatait. az SQL Server-példány** és **kattintson az alkalmaz**.  Ez adatbázis létrehozására az Azure Functions Futtatókörnyezettel sorrendben támogatásához szükséges a futtatókörnyezetben.
    
    ![Az Azure Functions futásidejű Preview adatbázis konfigurációja][7]

1. Kattintson a **hitelesítő adatok** fülre.  Ezen a képernyőn létre kell hoznia két új hitelesítő adatokat a fájlmegosztás az Azure Functions üzemeltetéséhez.  **Adja meg a felhasználónevet és jelszót** kombinációk a **megosztás fájltulajdonos** és a **fájl megosztási felhasználói** kattintson **alkalmaz**.

    ![Az Azure Functions futásidejű Preview hitelesítő adatok][8]

1. Kattintson a **fájlmegosztás** fülre.  Ez a képernyő meg kell adnia a részleteit a **fájlmegosztás helye**.  Ez az Ön hozható létre, vagy egy meglévő fájlmegosztást, és kattintson a **alkalmaz**.  Ha új fájlmegosztás helyét, adjon meg egy könyvtárat az Azure Functions futtatókörnyezete használja.
    
    ![Az Azure Functions futásidejű Preview fájlmegosztás][9]

1. Kattintson a **IIS** fülre.  Ezen a lapon, amely az Azure funkciók Runtime telepítésének hozza létre az IIS a webhelyek részleteit jeleníti meg.  **Kattintson az alkalmaz** befejezéséhez.

    ![Az Azure Functions futásidejű előzetes IIS][10]

1. Kattintson a **szolgáltatások** fülre.  Ezen a lapon, a szolgáltatások állapotát jeleníti meg az Azure Functions Futtatókörnyezettel telepítésében.  Ha a kezdeti konfigurációt követően a **Azure Functions gazdaszolgáltatás aktiválási** nem fut kattintson **szolgáltatás indítása**

    ![Az Azure Functions futásidejű Preview konfigurációs befejezése][11]

1. Végül keresse meg a **Azure Functions futásidejű portálon** ,`https://<machinename>/`

    ![Az Azure Functions futásidejű a betekintő portálon][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png