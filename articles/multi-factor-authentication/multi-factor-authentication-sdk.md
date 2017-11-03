---
title: "Többtényezős hitelesítés software development kit egyéni alkalmazásokba |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan töltse le és engedélyezze a kétlépéses ellenőrzést, az egyéni alkalmazások az Azure MFA SDK segítségével."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 1c152f67-be02-42a5-a0c7-246fb6b34377
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: kgremban
ms.openlocfilehash: 281f9c61a30a20027f69808600373aa272255ef6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Building a multi-factor Authentication egyéni alkalmazásokba (SDK)

Az Azure multi-factor Authentication Software Development Kit (SDK) lehetővé teszi a kétlépéses ellenőrzést közvetlenül folyamatokba a bejelentkezés vagy a tranzakció alkalmazásai az Azure AD-bérlő létrehozása.

A multi-factor Authentication SDK C#, Visual Basic (.NET), Java, Perl, PHP és Ruby érhető el. Az SDK-t kínál egy vékony kétlépéses ellenőrzést. Minden kell írnia a kódot, beleértve a megjegyzésként forráskódfájl, például fájlok és a részletes információs fájl tartalmazza. Minden SDK a tanúsítvány és titkos kulcsot, a többtényezős hitelesítési szolgáltató egyedi tranzakciók is tartalmaz. Mindaddig, amíg még meg olyan szolgáltatót, letöltheti az SDK legtöbb nyelveket és formátumban, szükség szerint.

A multi-factor Authentication SDK API-k szerkezete felettébb egyszerű. Győződjön meg a multi-factor Authentication beállítás paraméterek (például a hitelesítési mód) és a felhasználói adatokat (például a telefonszám vagy a PIN-kód érvényesítése) API hívása egy funkcióval. Az API-k lefordítani a felhőalapú Azure multi-factor Authentication szolgáltatást a webes szolgáltatások kérelmek függvény hívása. Az összes hívás tartalmaznia kell egy hivatkozás, amely minden SDK megtalálható titkos tanúsítványra.

Mivel az API-k nincs hozzáférése a felhasználók számára az Azure Active Directoryban regisztrálva, meg kell adni egy fájl vagy az adatbázis felhasználói adatokat. Is az API-k nem tartalmaz regisztrációs vagy a felhasználó felügyeleti funkciókat, ezért kell ezeket a folyamatokat az alkalmazásba.

> [!IMPORTANT]
> Az SDK letöltéséhez létre kell hoznia egy Azure Multi-Factor Auth szolgáltatót, akkor is, ha rendelkezik Azure MFA, AAD Premium vagy EMS licencekkel. Ha erre a célra az Azure többtényezős hitelesítésszolgáltató létrehozása, és már rendelkezik licenccel, ügyeljen arra, hogy a szolgáltató létrehozása a **Per Enabled User** modell. Ezt követően kapcsolja a szolgáltatót az Azure MFA, Azure AD Premium vagy EMS licenceket tartalmazó könyvtárhoz. Ez a konfiguráció biztosítja, hogy Ön csak számlázása Amennyiben több egyedi felhasználók, mint a saját licencek száma a SDK használatával.


## <a name="download-the-sdk"></a>Az SDK letöltése
Az Azure multi-factor Authentication SDK letöltése szükséges egy [Azure többtényezős hitelesítésszolgáltató](multi-factor-authentication-get-started-auth-provider.md).  Ehhez a teljes Azure-előfizetéssel, akkor is, ha az Azure MFA, az Azure AD Premium vagy a nagyvállalati mobilitási csomag licencek a tulajdonosa.  Az SDK letöltéséhez keresse meg a multi-factor Authentication kezelési portál. Közvetlenül a többtényezős hitelesítésszolgáltató kezelése, vagy kattintson a portál képes elérni a **"Nyissa meg a portál"** hivatkozás a multi-factor Authentication szolgáltatás beállítások lapon.

### <a name="download-from-the-azure-classic-portal"></a>Töltse le a klasszikus Azure portálon
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. Az Active Directory oldalon, a felső válassza **többtényezős hitelesítésszolgáltatók**
4. A lap alján válassza **kezelése**. Megnyílik egy új lap.
5. Kattintson a bal alsó, **SDK**.
   <center>![Letöltése](./media/multi-factor-authentication-sdk/download.png)</center>
6. Válassza ki a nyelvet, majd kattintson az egyik a letöltési hivatkozás.
7. Mentse a letöltött fájlt.

### <a name="download-from-the-service-settings"></a>Töltse le a szolgáltatás beállításai
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. Kattintson duplán az Azure AD-példányra.
4. A lap tetején kattintson a **Konfigurálás** elemre.
5. Válassza ki a multi-factor Authentication hitelesítés **szolgáltatás beállításainak kezelése**
   ![letöltése](./media/multi-factor-authentication-sdk/download2.png)
6. A Szolgáltatásbeállítások lapon, a képernyő alsó részén kattintson az **Ugrás a portálra** elemre. Megnyílik egy új lap.
   ![Letöltés](./media/multi-factor-authentication-sdk/download3a.png)
7. Kattintson a bal alsó, **SDK**.
8. Válassza ki a nyelvet, majd kattintson az egyik a letöltési hivatkozás.
9. Mentse a letöltött fájlt.

## <a name="whats-in-the-sdk"></a>Mi az az SDK-ban
Az SDK-t a következőket tartalmazza:

* **INFORMÁCIÓS**. Új vagy meglévő alkalmazásokban a multi-factor Authentication API-k használatát ismerteti.
* **Forrásfájlokat** a többtényezős hitelesítés
* **Ügyféltanúsítvány** , amelyekkel a multi-factor Authentication szolgáltatással folytatott kommunikációhoz
* **Titkos kulcs** a tanúsítvány
* **Hívás eredményeit.** Hívási eredménykódok listáját. Ez a fájl megnyitásához egy alkalmazás használatát formázás, például a WordPad szóra. A hívási eredménykódok segítségével tesztelése és hibakeresése a multi-factor Authentication végrehajtása az alkalmazásban. Nincsenek hitelesítési állapotkódok.
* **Példák.** A multi-factor Authentication használatának megvalósítási mintakód.

> [!WARNING]
> Az ügyféltanúsítvány akkor egyedi személyes tanúsítványt, amelyik kifejezetten jött létre. Ne ossza, vagy elveszíti a fájl. A kulcs biztonsága érdekében a multi-factor Authentication szolgáltatással folytatott kommunikáció biztosításához is.

## <a name="code-sample"></a>Kódminta
A fenti jeleníti meg az API-k használata az Azure multi-factor Authentication SDK a szabványos mód hang hívás ellenőrzési hozzá az alkalmazáshoz. Szabványos módban a telefonhívások, amely a # billentyűt lenyomásával válaszol a felhasználó.

Ez a példa a C# .NET 2.0 multi-factor Authentication SDK a C# kiszolgálóoldali logika egy egyszerű ASP.NET-alkalmazások, de a folyamat hasonlít más nyelveken. Az SDK tartalmaz forrásfájlokat, nem végrehajtható fájlok, mert a fájlok létrehozása és azok hivatkozik, vagy közé tartozik az azokat közvetlenül a.

> [!NOTE]
> Többtényezős hitelesítés megvalósításához, a további módszer segítségével (telefonhívást vagy SMS-t), másodlagos vagy harmadlagos ellenőrzési kiegészíti az elsődleges hitelesítési módszert (felhasználónév és jelszó). Ezek a módszerek nem elsődleges hitelesítési módszerek tervezték.

### <a name="code-sample-overview"></a>Kód a minta áttekintése
A mintakód egyszerű bemutató webalkalmazás # kulcs választ a telefonhívások segítségével ellenőrzi a felhasználó hitelesítése. A telefonhívás tényező szabványos mód néven a multi-factor Authentication szolgáltatásra.

Az ügyféloldali kódot nem tartalmazza a multi-factor Authentication-specifikus elemeket. A további hitelesítési tényezők függetlenek az elsődleges hitelesítés, mert a meglévő bejelentkezési felületen módosítása nélkül is hozzá. Az a multi-factor Authentication SDK API-k lehetővé teszik, hogy a felhasználói élmény testreszabásáról, de előfordulhat, hogy nem kell minden bármit módosíthat.

A kiszolgálóoldali kódot normál módú hitelesítést hozzáadja a 2. lépés. Létrehoz egy PfAuthParams objektum normál módú ellenőrzéshez szükséges paraméterekkel rendelkező: felhasználónév, telefon számát, és a mód és az elérési útját az ügyféltanúsítvány (CertFilePath), amelyre azonban szükség van minden egyes hívásban. Minden paraméterek PfAuthParams bemutatásához tekintse meg a példa az SDK-ban.

A következő kódot a PfAuthParams objektum átadása pf_authenticate() függvény. A visszatérési érték azt jelzi, a sikeres vagy sikertelen volt, a hitelesítés. A kimenő paraméterek callStatus és errorID, további hívás eredménye információkat tartalmaznak. A hívási eredménykódok szerepelnek a hívás eredménye fájlban az SDK-ban.

A minimális végrehajtása néhány sor is beírhatók. Azonban az éles kódban, hogy tartalmazhat kifinomultabb hibakezelés, az adatbázis további kódot és jobb felhasználói élményt.

### <a name="web-client-code"></a>Webalkalmazás ügyféloldali kódot
Webes ügyfél kódja egy bemutató lap a következő:

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Kiszolgálóoldali kódolás
Az alábbi kiszolgálóoldali kódban a multi-factor Authentication konfigurálva és futtassa a 2. Standard (MODE_STANDARD) módja a telefonhívások, amelyhez a felhasználó a # billentyűt lenyomásával válaszol.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class \_Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "5555555555";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = "Multi-Factor Authentication failed.";
                }
            }

        }
    }
