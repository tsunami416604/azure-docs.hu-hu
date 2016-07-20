<properties
    pageTitle="Azure Active Directory B2C előzetes verzió: Alkalmazás regisztrációja | Microsoft Azure"
    description="Hogyan regisztrálhatja alkalmazását Azure Active Directory B2C-vel?"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="swkrish"/>


# Azure Active Directory B2C előzetes verzió: Alkalmazás regisztrációja

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Előfeltételek

A felhasználói regisztrációt és bejelentkezést elfogadó alkalmazás létrehozásához először regisztrálnia kell az alkalmazást egy Azure Active Directory B2C-bérlővel. Hozzon létre egy saját bérlőt az [Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md) részben ismertetett lépések segítségével. Miután elvégezte ezeket a lépéseket, a B2C funkciók panelje rögzítésre kerül a kezdőpulton.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Lépjen a B2C funkciók paneljére

Az Azure portálról vagy a klasszikus Azure portálról léphet a B2C funkciók paneljére.

### 1. Elérés az Azure portálon

Ha a B2C funkciók panelje rögzítésre kerül a kezdőpulton, azonnal látni fogja a panelt, amint B2C-bérlő globális rendszergazdájaként bejelentkezik az [Azure portálra](https://portal.azure.com/).

A panelre úgy is beléphet, hogy a **Browse** (Tallózás), majd az **Azure AD B2C** lehetőségre kattint az [Azure portál](https://portal.azure.com/) bal oldali navigációs ablaktáblájában.

A panelhez közvetlenül a [https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/)hivatkozással is hozzáférhet úgy, hogy a **{tenant}** címke helyére a bérlő létrehozáskor használt nevet írja be (például: contosob2c). Ezt a hivatkozást későbbi használatra könyvjelző formájában elmentheti.

    > [AZURE.IMPORTANT]
    A B2C bérlő globális rendszergazdájának kell lennie ahhoz, hogy hozzáférhessen a B2C funkciók panelhez. Más bérlők globális rendszergazdái vagy felhasználói nem férhetnek hozzá a panelhez.

### 2. Hozzáférés a klasszikus Azure-portálhoz

Jelentkezzen be a [klasszikus Azure-portálra](https://manage.windowsazure.com/) előfizetés-rendszergazdaként. (Ez ugyanaz a munkahelyi, iskolai vagy Microsoft-fiók, amelyet az Azure-ba történő regisztráláshoz használt.) Lépjen a bal oldalon található Active Directory bővítményre, majd kattintson a B2C bérlőre. Az **Quick Start** (Első lépések) lapon (az első megnyíló lapon) kattintson a **Manage B2C settings** (B2C-beállítások kezelése) lehetőségre az **Administer** (Felügyelet) szakaszban. Ekkor a B2C funkciók panelje megnyílik egy új böngészőablakban vagy lapon.

A **Manage B2C settings** (B2C beállítások kezelése) hivatkozást a **B2C Administration** (B2C felügyelet) szakaszban is megtalálhatja a **Configure** (Konfigurálás) fülön.

## Egy alkalmazás regisztrálása

1. Kattintson az Azure portál B2C funkciók panelén az **Applications** (Alkalmazások) lehetőségre.
2. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
3. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Adja meg például a „Contoso B2C alkalmazás” nevet.
4. Ha webalapú alkalmazásról van szó, az **Include web app / web API** (Webalkalmazással/webes API-val együtt) váltógombot állítsa a **Yes** (Igen) állásba. A **válasz URL-címek** olyan végpontok, ahol az Azure AD B2C visszalép az alkalmazás által kért jogkivonatokhoz. Adja meg például a következőt: `https://localhost:44321/`. Ha az alkalmazás olyan kiszolgálóoldali összetevőt (API) tartalmaz, amelyet védeni kell, akkor hozzon létre (és másoljon) egy **alkalmazástitkot** a **Generate key** (Kulcs létrehozása) gombra kattintva.

    > [AZURE.NOTE]
Az     **alkalmazástitok** fontos biztonsági hitelesítő adat.

5. Ha mobilalkalmazásról van szó, az **Include native client** (Natív ügyféllel együtt) váltógombot állítsa a **Yes** (Igen) állásba. Másolja le az automatikusan létrehozott, alapértelmezett **átirányítási URI**-t.
6. Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.
7. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni.

## Első lépések alkalmazás készítése

Miután az Azure AD B2C-vel regisztrált egy alkalmazást, gyors üzembe helyezési oktatóanyagunk segítségével elsajátíthatja a használatát. Az alábbiakban néhány javaslatot olvashat:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]



<!--HONumber=Jun16_HO2-->


