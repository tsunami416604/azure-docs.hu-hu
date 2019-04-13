---
title: Az Azure Functions Runtime telepítésének |} A Microsoft Docs
description: Az Azure Functions Runtime előzetes 2 telepítése
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: aae6bc41f3c2fc2c5f8cf63d07f6b4d79bb3564a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547548"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Az Azure Functions Runtime előzetes 2 telepítése

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Ha szeretné telepíteni az Azure Functions Runtime előzetes 2, kövesse az alábbi lépéseket:

1. Győződjön meg arról, a gép megfelel a minimális követelményeknek.
1. Töltse le a [az Azure Functions Runtime előzetes telepítő](https://aka.ms/azafrv2).
1. Távolítsa el az Azure Functions Runtime előzetes verziójának 1.
1. Telepítse az Azure Functions Runtime előzetes 2.
1. Az Azure Functions Runtime előzetes 2 konfigurálásának befejezéséhez.
1. Az első függvény létrehozása az Azure Functions Runtime előzetes verziója

## <a name="prerequisites"></a>Előfeltételek

Mielőtt telepíti az Azure Functions Runtime előzetes verzióját, a következő rendelkezésre álló erőforrások kell rendelkeznie:

1. A Microsoft Windows Server 2016 vagy (Professional vagy Enterprise Edition) a Microsoft Windows 10 alkotói frissítését futtató gép.
1. A hálózaton belül futó SQL Server-példányt.  Minimálisan szükséges kiadása az SQL Server Express.

## <a name="uninstall-previous-version"></a>Távolítsa el az előző verzió

Ha korábban már telepítette az Azure Functions Runtime előzetes verzióját, el kell távolítania a legújabb kiadás telepítése előtt.  Távolítsa el az Azure Functions Runtime előzetes verzióját a Programok telepítése/törlése a Windows a program eltávolításával.

## <a name="install-the-azure-functions-runtime-preview"></a>Telepítse az Azure Functions Runtime előzetes verziója

A Azure Functions Runtime előzetes telepítő végigvezeti Önt az Azure Functions Runtime előzetes felügyeleti és a feldolgozói szerepkörök telepítését.  A felügyeleti és a feldolgozói szerepkör telepítése ugyanarra a gépre lehetőség.  További függvényalkalmazások hozzáadásakor, további feldolgozó szerepkört üzemeltetve tudják feldolgozó alakzatot a functions méretezése további gépeket kell telepítenie.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>A felügyeleti és a feldolgozói szerepkör telepítése ugyanarra a gépre

1. Futtassa a az Azure Functions Runtime előzetes telepítőt.

    ![Az Azure Functions Runtime előzetes telepítő][1]

1. Kattintson a **tovább**.
1. Miután elolvasta a feltételeket, a **EULA**, **jelölje be a jelölőnégyzetet** fogadja el a feltételeket, és kattintson a **tovább** , azt mutatja be.
1. Válassza ki a szerepköröket, telepítse a gépen szeretné **funkciók felügyeleti szerepkör** és/vagy **funkciók feldolgozói szerepkör** kattintson **tovább**.

    ![Az Azure Functions Runtime előzetes telepítő – szerepkör kiválasztása][3]

    > [!NOTE]
    > Telepítheti a **funkciók feldolgozói szerepkör** sok más számítógépre. Ehhez kövesse az alábbi utasításokat, és csak válassza **funkciók feldolgozói szerepkör** a telepítőjében.

1. Kattintson a **tovább** szeretné, hogy a **Azure Functions Runtime telepítővarázsló** a gépen a telepítési folyamat elindításához.
1. Ha elkészült, a varázsló elindítja a **Azure Functions Runtime** konfigurációs eszközt.

    ![Az Azure Functions Runtime előzetes telepítő befejezése][6]

    > [!NOTE]
    > Ha telepíti a **Windows 10-es** és a **tároló** a szolgáltatás nem lett korábban engedélyezte, a **Azure Functions Runtime telepítővarázslójának befejező** felszólítja, hogy indítsa újra a gépet a telepítés befejezéséhez.

## <a name="configure-the-azure-functions-runtime"></a>Az Azure Functions futtatókörnyezet konfigurálása

Az Azure Functions Runtime telepítésének befejezéséhez el kell végeznie a konfigurációt.

1. A **Azure Functions Runtime** konfigurációs eszköz megjeleníti, hogy milyen szerepkörök vannak telepítve a gépen.

    ![Az Azure Functions Runtime előzetes konfigurálása eszköz][7]

1. Kattintson a **adatbázis** lapra, adja meg, hogy kapcsolatot az SQL Server-példány, beleértve a megadása egy [adatbázisfőkulcs](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), és kattintson a **alkalmaz**.  Az SQL Server-példányra kapcsolat szükséges az adatbázis létrehozása az Azure Functions Runtime ahhoz, hogy a futtatókörnyezet támogatja.

    ![Az Azure Functions Runtime előzetes adatbázis konfigurálása][8]

1. Kattintson a **hitelesítő adatok** fülre.  Itt a fájlmegosztással a függvényalkalmazásainak üzemeltetéséhez létre kell hoznia két új hitelesítő adatokat használja.  Adja meg **felhasználónév** és **jelszó** -kombinációk a **fájlmegosztás tulajdonosa** és a a **fájlmegosztás felhasználója**, majd kattintson a **Alkalmazása**.

    ![Az Azure Functions Runtime előzetes hitelesítő adatok][9]

1. Kattintson a **fájlmegosztás** fülre.  Itt adja meg a fájlmegosztási helyet adatait.  A fájlmegosztás hozható létre az Ön számára, vagy használhat egy meglévő fájlmegosztási és kattintson a **alkalmaz**.  Ha új fájlmegosztás helyét, adjon meg egy könyvtárat használja az Azure Functions Runtime.

    ![Az Azure Functions Runtime előzetes fájlmegosztás][10]

1. Kattintson a **IIS** fülre.  Ezen a lapon az IIS-ben, amely az Azure Functions Runtime konfigurálása eszköz létrehozza a webhelyeket részleteit jeleníti meg.  Megadhat egy egyéni DNS-nevét itt az Azure Functions Runtime előzetes portál.  Kattintson a **alkalmaz** befejezéséhez.

    ![Az Azure Functions Runtime előzetes verziója az IIS][11]

1. Kattintson a **szolgáltatások** fülre.  Ezen a lapon a szolgáltatások állapotát az Azure Functions Runtime konfigurálása eszköz jeleníti meg.  Ha a **Azure Functions gazdaszolgáltatása aktiválási** van a kezdeti konfigurációt követően nem fut, kattintson a **szolgáltatás indítása**.

    ![Az Azure Functions Runtime előzetes konfigurálás befejezése][12]

1. Keresse meg a **az Azure Functions Runtime portál** , `https://<machinename>.<domain>/`.

    ![Az Azure Functions Runtime betekintő portálon][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Az első függvény létrehozása az Azure Functions Runtime előzetes verziója

Az Azure Functions Runtime előzetes verzióban érhető el az első függvény létrehozása

1. Keresse meg a **Azure Functions Runtime portálon** , `https://<machinename>.<domain>` például `https://mycomputer.mydomain.com`.

1. Kéri **bejelentkezés**, ha a tartományt használja a tartományi fiók felhasználónevét és jelszavát, egyéb módon használja a helyi fiók felhasználónevét és jelszavát jelentkezzen be a portálra telepített.

    ![Az Azure Functions Runtime előzetes portál bejelentkezési][14]

1. Függvényalkalmazás létrehozásához, létre kell hoznia egy előfizetést.  A portál bal felső sarkában kattintson a **+** mellett az előfizetések lehetőséget.

    ![Az Azure Functions Runtime előzetes portál előfizetések][15]

1. Válasszon **DefaultPlan**, adja meg az előfizetés nevét, majd kattintson **létrehozás**.

    ![Az Azure Functions Runtime előzetes portál előfizetési csomagot és neve][16]

1. Minden függvény a portál bal oldali ablaktáblájában láthatók.  Hozzon létre egy új Függvényalkalmazást, kattintson a fejléc **Függvényalkalmazások** , és kattintson a **+** lehetőséget.

1. Adja meg a függvényalkalmazás nevét, válassza ki a megfelelő előfizetést, válassza ki az Azure Functions futtatókörnyezettel programozni, és kattintson a kívánt melyik verzióját **létrehozása**

    ![Az Azure Functions Runtime előzetes portál új függvényalkalmazás][17]

1. Az új függvényalkalmazást szerepel a portál bal oldali ablaktáblájában.  Válassza ki a Funkciók, és kattintson a **új függvény** a középső ablaktáblán a portál tetején.

    ![Az Azure Functions Runtime előzetes sablonok][18]

1. Válassza ki az időzítő eseményindító függvényt, a függvény neve a jobb oldali úszó menü, és módosítsa az ütemezést, hogy `*/5 * * * * *` (Ez cron-kifejezés okoz az időzítő függvény végrehajtására öt másodpercenként), és kattintson a **létrehozása**

    ![Az Azure Functions Runtime előzetes új időzítő függvény konfigurálása][19]

1. A függvény létrehozása megtörtént.  Megtekintheti a végrehajtási naplóból a függvényalkalmazás által bővítése a **log** panelen a portál alján.

    ![Az Azure Functions Runtime előzetes függvény végrehajtása][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
