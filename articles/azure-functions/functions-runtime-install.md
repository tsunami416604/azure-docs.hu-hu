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
ms.openlocfilehash: b6078ba7b553773294ebbf11949f7d3b53f46f0c
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Telepítse az Azure Functions futásidejű előnézete

Ha szeretné telepíteni az Azure Functions Futtatókörnyezettel előzetes, akkor kövesse az alábbi lépéseket:

1. Győződjön meg arról, a gép megfelel a minimális követelményeknek.
1. Töltse le a [Azure Functions futásidejű Preview telepítő](https://aka.ms/azafr).
1. Telepítse az Azure Functions Futtatókörnyezettel előzetes.
1. Az Azure Functions Futtatókörnyezettel előzetes konfigurációjának befejezéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Functions Futtatókörnyezettel előzetes verzió telepítése előtt az alábbiakkal kell rendelkeznie:

1. A Microsoft Windows Server 2016 vagy a Microsoft Windows 10 Creators frissítés (Professional és Enterprise Edition) rendszerű gépek.
1. A hálózaton belül futó SQL Server-példányt.  A rendszer szükséges minimális edition SQL Server Express.

## <a name="install-the-azure-functions-runtime-preview"></a>Telepítse az Azure Functions futásidejű előnézete

A Azure Functions futásidejű Preview telepítő végigvezeti Önt az Azure Functions Futtatókörnyezettel előzetes felügyeleti és feldolgozói szerepkörök telepítését.  Akkor lehet a felügyeleti és a feldolgozói szerepkör telepítése ugyanazon a számítógépen.  Azonban további funkciók hozzáadása, telepítenie kell a további gépek alakzatot több Worker a functions méretezése tennie további feldolgozói szerepkörök is.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>A felügyeleti és a feldolgozói szerepkör telepítése ugyanazon a számítógépen

1. Futtassa az Azure Functions futásidejű Preview telepítőjét.

    ![Az Azure Functions futásidejű Preview telepítő][1]

1. Kattintson a **Tovább** gombra.
1. Ha mindenképpen olvassa el a használati a **EULA**, **jelölje be a jelölőnégyzetet** elfogadja a feltételeket, majd **következő** ahhoz, hogy.
1. Most válassza ki a szerepköröket, ezen a számítógépen telepítendő **funkciók szerepkör** és/vagy **funkciók feldolgozói szerepkör** kattintson **következő**.

    ![Az Azure Functions futásidejű Preview Installer - szerepkör kiválasztása][3]

    > [!NOTE]
    > Telepítheti a **funkciók feldolgozói szerepkör** sok más számítógépre. Ehhez kövesse ezeket az utasításokat, és csak **funkciók feldolgozói szerepkör** a telepítőben.

1. Kattintson a **következő** kell rendelkeznie a **Azure Functions futásidejű telepítővarázsló** a gépen a telepítés megkezdéséhez.
1. A telepítő varázsló elindítja művelet befejeződése után a **Azure Functions Futtatókörnyezettel** konfigurációs eszközt.

    ![Az Azure Functions futásidejű Preview telepítő befejezése][5]

    > [!NOTE]
    > Ha telepíti az **Windows 10** és a **tároló** funkció nem korábban engedélyezve van, a **Azure Functions Runtime telepítését** kéri, hogy indítsa újra a számítógépet a telepítés befejezéséhez.

## <a name="configure-the-azure-functions-runtime"></a>Az Azure Functions Futtatókörnyezettel konfigurálása

Az Azure Functions Futtatókörnyezettel telepítéséhez a konfigurációt kell végrehajtania.

1. A **Azure Functions Futtatókörnyezettel** konfigurációs eszköz jeleníti meg, mely szerepkörök sincs telepítve a számítógépen.

    ![Az Azure Functions futásidejű Preview konfigurációs eszköz][6]

1. Kattintson a **adatbázis** lapon adja meg a kapcsolati adatokat az SQL Server-példányhoz, majd kattintson **alkalmaz**.  Ez szükséges ahhoz, hogy az Azure Functions Futtatókörnyezettel adatbázist hozhat létre a futtatókörnyezet támogatásához.
    
    ![Az Azure Functions futásidejű Preview adatbázis konfigurációja][7]

1. Kattintson a **hitelesítő adatok** fülre.  Ezen a képernyőn létre kell hoznia két új hitelesítő adatokat a fájlmegosztás az Azure Functions üzemeltetéséhez.  Adja meg **felhasználónév** és **jelszó** kombinációk a **megosztás fájltulajdonos** és a a **fájl megosztási felhasználói**, majd kattintson az **Alkalmazása**.

    ![Az Azure Functions futásidejű Preview hitelesítő adatok][8]

1. Kattintson a **fájlmegosztás** fülre.  Ez a képernyő meg kell adnia a részleteit a **fájlmegosztás** helyét.  Ez az Ön hozható létre, vagy egy meglévő fájlmegosztást, és kattintson a **alkalmaz**.  Ha új fájlmegosztás helyét, adjon meg egy könyvtárat az Azure Functions futtatókörnyezete használja.
    
    ![Az Azure Functions futásidejű Preview fájlmegosztás][9]

1. Kattintson a **IIS** fülre.  Ezen a lapon az IIS-ben, amely az Azure Functions Futtatókörnyezettel konfigurálása eszköz létrehozza a webhelyek részleteit jeleníti meg.  Kattintson a **alkalmaz** befejezéséhez.

    ![Az Azure Functions futásidejű előzetes IIS][10]

1. Kattintson a **szolgáltatások** fülre.  Ezen a lapon, a szolgáltatások állapotát jeleníti meg az Azure Functions Futtatókörnyezettel konfigurációs eszközében.  Ha a kezdeti konfigurációt követően a **Azure Functions gazdaszolgáltatás aktiválási** nem fut kattintson **szolgáltatás indítása**.

    ![Az Azure Functions futásidejű Preview konfigurációs befejezése][11]

1. Végül keresse meg a **Azure Functions futásidejű portálon** , `https://<machinename>/`.

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
