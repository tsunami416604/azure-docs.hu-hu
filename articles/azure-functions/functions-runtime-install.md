---
title: "Azure Functions Runtime telepítésének |} Microsoft Docs"
description: "Az Azure Functions Futtatókörnyezettel előzetes 2 telepítése"
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
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: f8ce27bf28f73818932f2ac9056d4fdd573679e8
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Telepítse az Azure Functions Futtatókörnyezettel előzetes 2

Ha azt szeretné, az Azure Functions Futtatókörnyezettel preview 2 telepítéséhez, kövesse az alábbi lépéseket:

1. Győződjön meg arról, a gép megfelel a minimális követelményeknek.
1. Töltse le a [Azure Functions futásidejű Preview telepítő](https://aka.ms/azafrv2).
1. Távolítsa el az Azure Functions Futtatókörnyezettel preview 1.
1. Telepítse az Azure Functions Futtatókörnyezettel előzetes 2.
1. Az konfigurálása az Azure Functions Futtatókörnyezettel Preview 2.
1. Az első függvényét az Azure Functions futásidejű előzetes létrehozása

## <a name="prerequisites"></a>Előfeltételek

Az Azure Functions Futtatókörnyezettel előzetes verzió telepítése előtt rendelkeznie kell a következő rendelkezésre álló erőforrások:

1. A Microsoft Windows Server 2016 vagy a Microsoft Windows 10 Creators frissítés (Professional és Enterprise Edition) rendszerű gépek.
1. A hálózaton belül futó SQL Server-példányt.  A rendszer szükséges minimális edition SQL Server Express.

## <a name="uninstall-previous-version"></a>Korábbi verziójának eltávolítása

Ha korábban már telepítette az Azure Functions Futtatókörnyezettel előzetes, el kell távolítania a legújabb kiadás telepítése előtt.  Távolítsa el az Azure Functions Futtatókörnyezettel előzetes a program eltávolítja a Windows programok telepítése/törlése.

## <a name="install-the-azure-functions-runtime-preview"></a>Telepítse az Azure Functions futásidejű előnézete

A Azure Functions futásidejű Preview telepítő végigvezeti Önt az Azure Functions Futtatókörnyezettel előzetes felügyeleti és feldolgozói szerepkörök telepítését.  Akkor lehet a felügyeleti és a feldolgozói szerepkör telepítése ugyanazon a számítógépen.  Azonban további függvény alkalmazások hozzáadásakor, telepítenie kell a további feldolgozói szerepkörök további gépeket több Worker alakzatot a functions méretezése tennie a is.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>A felügyeleti és a feldolgozói szerepkör telepítése ugyanazon a számítógépen

1. Futtassa az Azure Functions futásidejű Preview telepítőjét.

    ![Az Azure Functions Futtatókörnyezettel preview telepítő][1]

1. Kattintson a **Tovább** gombra.
1. Ha mindenképpen olvassa el a használati a **EULA**, **jelölje be a jelölőnégyzetet** elfogadja a feltételeket, majd **következő** ahhoz, hogy.
1. Válassza ki a szerepköröket, ezen a számítógépen telepítendő **funkciók szerepkör** és/vagy **funkciók feldolgozói szerepkör** kattintson **következő**.

    ![Az Azure Functions Futtatókörnyezettel preview installer - szerepkör kiválasztása][3]

    > [!NOTE]
    > Telepítheti a **funkciók feldolgozói szerepkör** sok más számítógépre. Ehhez kövesse ezeket az utasításokat, és csak **funkciók feldolgozói szerepkör** a telepítőben.

1. Kattintson a **következő** kell rendelkeznie a **Azure Functions futásidejű telepítővarázsló** a gépen a telepítés megkezdéséhez.
1. Művelet befejeződése után a telepítő varázsló elindítja a **Azure Functions Futtatókörnyezettel** konfigurációs eszközt.

    ![Az Azure Functions Futtatókörnyezettel preview telepítő befejezése][6]

    > [!NOTE]
    > Ha telepíti az **Windows 10** és a **tároló** funkció nem korábban engedélyezve van, a **Azure Functions Runtime telepítését** kéri, hogy indítsa újra a számítógépet a telepítés befejezéséhez.

## <a name="configure-the-azure-functions-runtime"></a>Az Azure Functions Futtatókörnyezettel konfigurálása

Az Azure Functions Futtatókörnyezettel telepítéséhez a konfigurációt kell végrehajtania.

1. A **Azure Functions Futtatókörnyezettel** konfigurációs eszköz jeleníti meg, mely szerepkörök sincs telepítve a számítógépen.

    ![Az Azure Functions Futtatókörnyezettel preview konfigurációs eszköz][7]

1. Kattintson a **adatbázis** lapra, adja meg a kapcsolati adatokat az SQL Server-példányhoz, beleértve a megadása egy [adatbázis főkulcsának](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), és kattintson a **alkalmaz**.  Egy SQL Server-példányhoz való kapcsolat ahhoz, hogy az Azure Functions Futtatókörnyezettel adatbázist hozhat létre a futtatókörnyezet támogatásához szükséges.

    ![Az Azure Functions Futtatókörnyezettel preview adatbázis konfigurációja][8]

1. Kattintson a **hitelesítő adatok** fülre.  Itt létre kell hoznia két új hitelesítő adatokat egy fájlmegosztást, a függvény minden alkalmazás üzemeltetéséhez.  Adja meg **felhasználónév** és **jelszó** kombinációk a **megosztás fájltulajdonos** és a a **fájl megosztási felhasználói**, majd kattintson az **Alkalmazása**.

    ![Az Azure Functions Futtatókörnyezettel preview hitelesítő adatok][9]

1. Kattintson a **fájlmegosztás** fülre.  Itt adhatja meg a fájlmegosztási helyet adatait.  A fájlmegosztás hozható létre, vagy egy meglévő fájlmegosztás használja, és kattintson a **alkalmaz**.  Ha új fájlmegosztás helyét, adjon meg egy könyvtárat az Azure Functions futtatókörnyezete használja.

    ![Az Azure Functions Futtatókörnyezettel preview fájlmegosztás][10]

1. Kattintson a **IIS** fülre.  Ezen a lapon az IIS-ben, amely az Azure Functions Futtatókörnyezettel konfigurálása eszköz létrehozza a webhelyek részleteit jeleníti meg.  Előfordulhat, hogy adjon meg egy egyéni DNS-nevet itt az Azure Functions Futtatókörnyezettel betekintő portálon.  Kattintson a **alkalmaz** befejezéséhez.

    ![Az Azure Functions Futtatókörnyezettel előzetes IIS][11]

1. Kattintson a **szolgáltatások** fülre.  Ezen a lapon, a szolgáltatások állapotát jeleníti meg az Azure Functions Futtatókörnyezettel konfigurációs eszközében.  Ha a **Azure Functions gazdaszolgáltatás aktiválási** van a kezdeti konfigurálás után nem fut, kattintson a **szolgáltatás indítása**.

    ![Az Azure Functions Futtatókörnyezettel preview konfigurálás befejezése][12]

1. Keresse meg a **Azure Functions futásidejű Portal** , `https://<machinename>.<domain>/`.

    ![Az Azure Functions Futtatókörnyezettel a betekintő portálon][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Az első függvényét az Azure Functions Futtatókörnyezettel előzetes létrehozása

Az első függvényét az Azure Functions Futtatókörnyezettel előzetes létrehozása

1. Keresse meg a **Azure Functions futásidejű Portal** , https://<machinename>.<domain> például https://mycomputer.mydomain.com
1. Kéri **jelentkezzen be**, ha egy tartomány használja, a tartományi fiók felhasználónevét és jelszavát, vagy használja a helyi fiók felhasználónevét és jelszavát jelentkezzen be a portálra telepített.

![Azure Functions Futtatókörnyezettel betekintő portál bejelentkezési][14]

1. Függvény-alkalmazásai létrehozására, létre kell hoznia egy előfizetést.  Kattintson a portál bal felső sarkában a  **+**  beállítás mellett a előfizetések

![Az Azure Functions futtatókörnyezete betekintő portál előfizetések][15]

1. Válasszon **DefaultPlan**, adjon meg egy nevet az előfizetéshez, és kattintson a **létrehozása**.

![Az Azure Functions Futtatókörnyezettel betekintő portál előfizetési csomagot és neve][16]

1. Minden, a függvény alkalmazások találhatók, a portál bal oldali ablaktáblán.  Hozzon létre egy új funkció alkalmazást, jelölje be a címsor **függvény alkalmazások** , és kattintson a  **+**  lehetőséget.

1. Adja meg a függvény alkalmazás nevét, válassza ki a megfelelő előfizetést, válassza ki az Azure Functions futtatókörnyezettel, szemben a programot, és kattintson a kívánt verziójának **létrehozása**

![Az Azure Functions Futtatókörnyezettel preview új függvény portálon][17]

1. Az új függvény alkalmazás szerepel a portál bal oldali ablaktáblán.  Válassza ki a Funkciók, és kattintson a **új függvény** felső részén a középső ablaktáblán a portálon.

![Az Azure Functions Futtatókörnyezettel preview sablonok][18]

1. Válassza ki a időzítő Indítófeltételt függvényt, a jobb oldali menü adja meg a függvény nevét, és módosítsa az ütemezés `*/5 * * * * *` (Ez cron-kifejezés az időzítő függvény végrehajtása, ötpercenként hatására), és kattintson a **létrehozása**

![Az Azure Functions Futtatókörnyezettel preview új időzítő függvény konfiguráció][19]

1. A függvény létrehozása megtörtént.  A végrehajtási naplóban, a függvény alkalmazás kibontásával megtekintheti a **napló** ablaktáblán, a portál alján.

![Az Azure Functions Futtatókörnyezettel preview függvény végrehajtása][20]

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
