---
title: Az Azure Functions futásidejű telepítése
description: Az Azure Functions Runtime preview 2 telepítése
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 7ad748aa9a5b45af10121648a668344548484cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226725"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Az Azure Functions Runtime preview 2 telepítése

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Ha telepíteni szeretné az Azure Functions Runtime preview 2-t, kövesse az alábbi lépéseket:

1. Győződjön meg róla, hogy a gép megfelel a minimális követelményeknek.
1. Töltse le az [Azure Functions Runtime Preview Installer](https://aka.ms/azafrv2)alkalmazást.
1. Távolítsa el az Azure Functions Runtime preview 1.Uninstall the Azure Functions Runtime preview 1.
1. Telepítse az Azure Functions Runtime preview 2-t.
1. Végezze el az Azure Functions Runtime preview 2 konfigurációját.
1. Az első funkció létrehozása az Azure Functions futásidejű előzetes verziójában

## <a name="prerequisites"></a>Előfeltételek

Az Azure Functions Runtime előzetes verzió telepítése előtt a következő erőforrásokkal kell rendelkeznie:

1. Microsoft Windows Server 2016 vagy Microsoft Windows 10 Creators Update (Professional vagy Enterprise Edition) rendszert futtató gép.
1. A hálózaton futó SQL Server-példány.  A minimális kiadás az SQL Server Express.

## <a name="uninstall-previous-version"></a>Előző verzió eltávolítása

Ha korábban már telepítette az Azure Functions Runtime előzetes verziót, el kell távolítania a legújabb kiadás telepítése előtt.  Távolítsa el az Azure Functions runtime előzetes verzióját a program eltávolításával a Windows Programok telepítése/törlése segédprogramjában.

## <a name="install-the-azure-functions-runtime-preview"></a>Az Azure Functions runtime előzetes verziójának telepítése

Az Azure Functions Runtime Preview Installer végigvezeti az Azure Functions runtime előzetes verziókezelési és feldolgozói szerepkörök telepítésén.  Lehetőség van a felügyeleti és a feldolgozói szerepkör ugyanazon a számítógépen telepíthető.  Azonban további függvényalkalmazások hozzáadásakor több feldolgozói szerepkört kell telepítenie további gépeken, hogy a függvényeket több dolgozóra méretezhet.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>A felügyeleti és a feldolgozói szerepkör telepítése ugyanarra a gépre

1. Futtassa az Azure Functions futásidejű előzetes telepítőt.

    ![Az Azure Functions futásidejű előzetes telepítője][1]

1. Kattintson a **Tovább** gombra.
1. Miután elolvasta a **feltételeket a EULA,** **jelölje be a jelölőnégyzetet,** hogy elfogadja a feltételeket, és kattintson a **Tovább** gombot előre.
1. Válassza ki a gépen telepíteni kívánt **szerepköröket, majd** kattintson a **Tovább**gombra. **Functions Worker Role**

    ![Azure Functions Runtime előzetes telepítő – szerepkör kiválasztása][3]

    > [!NOTE]
    > A Functions **Worker role** számos más gépre telepíthető. Ehhez kövesse az alábbi utasításokat, és csak a **kezelői szerepkör funkciók lehetőséget** válassza a telepítőben.

1. Kattintson a **Tovább** gombra, ha azt szeretné, hogy az **Azure Functions futásidejű telepítővarázsló** megkezdje a telepítési folyamatot a számítógépen.
1. Miután elkészült, a telepítő varázsló elindítja az **Azure Functions Runtime** konfigurációs eszközt.

    ![Az Azure Functions futásidejű előzetes telepítője kész][6]

    > [!NOTE]
    > Ha **windows 10-re** telepít, és a **Tároló** szolgáltatás korábban még nincs engedélyezve, az Azure **Functions Runtime setup** kéri, hogy indítsa újra a számítógépet a telepítés befejezéséhez.

## <a name="configure-the-azure-functions-runtime"></a>Az Azure Functions runtime konfigurálása

Az Azure Functions runtime telepítésének befejezéséhez be kell fejeznie a konfigurációt.

1. Az **Azure Functions Runtime** konfigurációs eszköz megmutatja, hogy mely szerepkörök vannak telepítve a gépen.

    ![Az Azure Functions Runtime előzetes konfigurációs eszköze][7]

1. Kattintson az **Adatbázis** fülre, adja meg az SQL Server-példány kapcsolatának részleteit, például adja meg [az Adatbázis-főkulcs kulcsát,](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)majd kattintson az Alkalmaz **gombra.**  Az SQL Server-példányhoz való kapcsolódás szükséges ahhoz, hogy az Azure Functions Runtime adatbázist hozzon létre a Futásidejű támogatásához.

    ![Az Azure Functions futásidejű előzetes adatbázis-konfigurációja][8]

1. Kattintson a **Hitelesítő adatok** fülre.  Itt két új hitelesítő adatot kell létrehoznia egy fájlmegosztással való használatra az összes függvényalkalmazás üzemeltetéséhez.  Adja meg **a felhasználónév** és **a jelszó** kombinációt a **fájlmegosztás tulajdonosának** és a **fájlmegosztás-felhasználónak,** majd kattintson az **Alkalmaz**gombra.

    ![Az Azure Functions futásidejű előzetes hitelesítő adatai][9]

1. Kattintson a **Fájlmegosztás** fülre.  Itt meg kell adnia a fájlmegosztás helyének részleteit.  A fájlmegosztás létrehozható, vagy használhat egy meglévő fájlmegosztást, és kattintson **az Alkalmaz**gombra.  Ha új fájlmegosztási helyet választ, meg kell adnia egy könyvtárat az Azure Functions Runtime számára.

    ![Azure Functions Runtime előzetes fájlmegosztás][10]

1. Kattintson az **IIS** fülre.  Ez a lap az Azure Functions Runtime konfigurációs eszköz által létrehozott IIS-ben található webhelyek részleteit jeleníti meg.  Itt megadhat egy egyéni DNS-nevet az Azure Functions Runtime preview portálhoz.  Kattintson **az Alkalmazás gombra** a befejezéshez.

    ![Az Azure Functions futásidejű előzetes verziójának IIS-e][11]

1. Kattintson a **Szolgáltatások** fülre.  Ez a lap az Azure Functions futásidejű konfigurációs eszköz szolgáltatásainak állapotát mutatja be.  Ha az **Azure Functions Host Activation Service** nem fut a kezdeti konfiguráció után, kattintson a Szolgáltatás **indítása**gombra.

    ![Az Azure Functions futásidejű előzetes konfigurációja befejeződött][12]

1. Tallózással keresse meg az **Azure Functions Runtime Portal** tallózva. `https://<machinename>.<domain>/`

    ![Az Azure Functions futásidejű előzetes portálja][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Az első funkció létrehozása az Azure Functions Runtime előzetes verziójában

Az első függvény létrehozása az Azure Functions Runtime előzetes verziójában

1. Tallózással keresse meg `https://<machinename>.<domain>` például `https://mycomputer.mydomain.com`az Azure Functions **Runtime Portal** tallózva.

1. Ha a tartományban van telepítve, jelentkezzen **be,** használja a tartományi fiók felhasználónevét és jelszavát, ellenkező esetben a helyi fiók felhasználónevét és jelszavát használja a portálra való bejelentkezéshez.

    ![Az Azure Functions Runtime előzetes portálbejelentkezési portálja][14]

1. Függvényalkalmazások létrehozásához létre kell hoznia egy Előfizetést.  A portál bal felső sarkában kattintson **+** az előfizetések melletti lehetőségre.

    ![Az Azure Functions Runtime előzetes portálelőfizetései][15]

1. Válassza **a DefaultPlan**( Alapértelmezett terv) lehetőséget, adja meg az előfizetés nevét, majd kattintson a **Létrehozás gombra.**

    ![Az Azure Functions Runtime előzetes portál előfizetési csomagja és neve][16]

1. Az összes függvényalkalmazás a portál bal oldali ablaktáblájában jelenik meg.  Új függvényalkalmazás létrehozásához jelölje ki a **függvényalkalmazások címsort,** és kattintson a **+** lehetőségre.

1. Adja meg a függvényalkalmazás nevét, válassza ki a megfelelő előfizetést, válassza ki, hogy az Azure Functions futásidejű melyik verziójához szeretne programozni, és kattintson a **Létrehozás gombra.**

    ![Azure Functions Runtime preview portal új függvényalkalmazás][17]

1. Az új függvényalkalmazás a portál bal oldali ablaktáblájában jelenik meg.  Válassza a Funkciók lehetőséget, majd kattintson az **Új függvény** elemre a portál középső ablaktábla tetején.

    ![Az Azure Functions futásidejű előzetes sablonjai][18]

1. Válassza ki az Időzítő trigger funkciót, a jobb oldali úszó `*/5 * * * * *` panelen a függvény nevét, és módosítsa az Ütemezés (ez a cron kifejezés okozza az időzítő funkció végrehajtása öt másodpercenként), és kattintson a **Create**

    ![Az Azure Functions Runtime preview új időzítőfunkció-konfigurációja][19]

1. A funkció létrehozása létrejött.  Megtekintheti a végrehajtási naplót a függvény alkalmazás kibontásával a **napló** ablaktábla alján a portálon.

    ![Az Azure Functions futásidejű előzetes verziója funkció végrehajtása][20]

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
