---
title: Az Azure MFA software development Kitet, az egyéni alkalmazások
description: Ez a cikk bemutatja, hogyan tölthet le és engedélyezze a kétlépéses ellenőrzést, az egyéni alkalmazások az Azure MFA SDK használatával.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 6b82ba53e7a469b01d77865831c2f5fb37f71044
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160841"
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Az egyéni alkalmazásokba (SDK) Building többtényezős hitelesítés

> [!IMPORTANT]
> Bejelentettük az Azure Multi-Factor Authentication szoftverfejlesztői készlet (SDK) elavulását. Ez a funkció már nem lesz támogatott az új ügyfelek. A jelenlegi ügyfelek 2018. november 14-ig használhatják az SDK-t. Ezután az SDK felé indított hívások meghiúsulnak. 

Az Azure multi-factor Authentication Software Development Kit (SDK) lehetővé teszi, hogy hozhat létre a kétlépéses ellenőrzést közvetlenül a bejelentkezési vagy tranzakciós folyamatokat az alkalmazások az Azure AD-bérlőben.

A multi-factor Authentication SDK C#, Visual Basic (.NET), a Java, Perl, a PHP és Ruby érhető el. Az SDK-t kínál egy dinamikus kiosztású kétlépéses ellenőrzést. Minden kell írnia a kódot, beleértve a megjegyzésekkel forráskódfájljait, példa fájlt és a egy részletes információs fájl tartalmazza. Az SDK is tartalmaz, egy tanúsítvány és titkos kulcsot, a multi-factor Authentication-szolgáltató egyedi. Mindaddig, amíg a szolgáltató rendelkezik, letöltheti az SDK legtöbb nyelvek és formátumban, igény szerint.

A multi-factor Authentication SDK-t az API-k szerkezete használata egyszerű. Győződjön meg a multi-factor Authentication beállítás paraméterek (például a hitelesítési mód) és a felhasználói adatok (például a telefonszám vagy érvényesíteni a PIN-kódot) API-hívás egy funkcióval. Az API-k fordítja le a függvény hívása a felhőalapú Azure multi-factor Authentication szolgáltatás webes szolgáltatások kérelmeivel be. Összes hívás tartalmaznia kell egy hivatkozást a privát tanúsítványt, amely minden SDK része.

Mivel az API-k nem rendelkeznek hozzáféréssel az Azure Active Directoryban regisztrált felhasználók, meg kell adnia egy fájl vagy az adatbázis felhasználói adatokat. Emellett az API-k nem biztosítanak regisztrációs vagy a felhasználó-felügyeleti funkciókat, így kell ezeket a folyamatokat építve az alkalmazásba.

> [!IMPORTANT]
> Az SDK letöltéséhez létre kell hoznia egy Azure Multi-Factor Auth szolgáltatót, akkor is, ha rendelkezik Azure MFA, AAD Premium vagy EMS licencekkel. Ha erre a célra az Azure multi-factor Auth szolgáltató létrehozása, és már rendelkezik licencekkel, ügyeljen arra, hogy a szolgáltató létrehozása a **engedélyezett felhasználónként** modell. Ezt követően kapcsolja a szolgáltatót az Azure MFA, Azure AD Premium vagy EMS licenceket tartalmazó könyvtárhoz. Ez a konfiguráció biztosítja, hogy csak a számlázás Ha több egyedi felhasználója használja az SDK-t, mint a saját licencek száma.


## <a name="download-the-sdk"></a>Az SDK letöltése
Az Azure multi-factor Authentication SDK letöltése szükséges egy [Azure multi-factor Auth szolgáltató](concept-mfa-authprovider.md).  Ehhez egy teljes körű Azure-előfizetéssel, akkor is, ha az Azure MFA, Azure AD prémium vagy nagyvállalati mobilitási csomag licenceket a tulajdonosa. A nyilvános módszerek az SDK letöltése rendelkezik már leszerelt, mivel az SDK elavult. A Microsoft kell nyitnia egy támogatási esetet, ha le kell töltenie az SDK-t. Az SDK-t csak az SDK-t már használó ügyfelek számára megadott. Új ügyfeleknek nem lesz regisztrálva.

## <a name="whats-in-the-sdk"></a>Mi az az SDK-ban
Az SDK a következőket tartalmazza:

* **INFORMÁCIÓS**. Ismerteti, hogyan használhatja a multi-factor Authentication hitelesítés API-k egy új vagy meglévő alkalmazásban.
* **Forrásfájlokat** a multi-factor Authentication
* **Ügyféltanúsítvány** , amellyel a multi-factor Authentication szolgáltatással folytatott kommunikációhoz
* **Titkos kulcs** a tanúsítvány
* **Hívás eredménye.** Hívási eredménykódok listája. Szöveg formázása, például a WordPad alkalmazás használata a fájl megnyitásához. A hívási eredménykódok használatával tesztelése és hibakeresése a multi-factor Authentication végrehajtása az alkalmazásban. Azok nem hitelesítési állapotkódok.
* **Példák.** A multi-factor Authentication szolgáltatás egy alapszintű működő megvalósítását mintakód.

> [!WARNING]
> Az ügyféltanúsítvány nem kifejezetten az Ön számára létrehozott egyedi privát tanúsítvány. Ne ossza, vagy elveszíti a fájl. Fontos a fiókkulcsot, hogy a multi-factor Authentication szolgáltatással folytatott kommunikáció biztonságát.

## <a name="code-sample"></a>Kódminta
A mintakód bemutatja, hogyan az Azure multi-factor Authentication SDK-t az API-k használatával adja hozzá az alkalmazáshoz szabványos mód hangalapú alapuló ellenőrzést kezdeményez. Normál módban, amely a # billentyűt lenyomásával válaszol a felhasználó telefonhívást.

Ebben a példában egy egyszerű ASP.NET-alkalmazás a C# kiszolgálóoldali logikát a C# .NET 2.0 multi-factor Authentication SDK-t használ, de a folyamat hasonlít az egyéb nyelveken. Az SDK tartalmaz forrásfájlokat, nem a végrehajtható fájlokat, mert a fájlok fejlesztését és hivatkozni tudjon rájuk, és belefoglalhatja őket közvetlenül az alkalmazás.

> [!NOTE]
> Multi-factor Authentication implementálásakor módokat kell használnia további (telefonhívás vagy SMS-t), a másodlagos vagy harmadlagos ellenőrzési az elsődleges hitelesítési módszert (felhasználónév és jelszó) lehetőséget. Ezek a módszerek nem lett kialakítva, elsődleges hitelesítési módszereként.

### <a name="code-sample-overview"></a>Kód minta áttekintése
A mintakód egy egyszerű bemutató webalkalmazás telefonhívást az # kulcs választ ad a felhasználói hitelesítés segítségével. Ez a hívás tényező a multi-factor Authentication szabványos mód néven ismert.

Az ügyféloldali kódot nem tartalmazza a multi-factor Authentication-specifikus elemeket. Mivel a további hitelesítési tényezők független az elsődleges hitelesítés, felveheti őket a meglévő bejelentkezési felület módosítása nélkül. A multi-factor Authentication SDK-t az API-k segítségével testre szabhatja a felhasználói élményt, de előfordulhat, hogy nem kell minden bármin változtatni.

A kiszolgálóoldali kód hozzáadja a normál módú hitelesítést a 2. lépés. Azt PfAuthParams objektumot hoz létre, amelyek a normál módú ellenőrzéshez szükséges paraméterekkel: felhasználónév, telefon, számot, és mód és az ügyféltanúsítvány (CertFilePath), amely azonban szükséges minden egyes hívásban elérési útját. Minden paraméter PfAuthParams bemutatójáért tekintse meg a példafájl az SDK-ban.

Ezután a kód a PfAuthParams objektum továbbítja a pf_authenticate() függvény. A visszaadott érték azt jelzi, hogy a sikeres vagy sikertelen a hitelesítés. A paraméterek, callStatus és errorID, további hívás eredménye információkat tartalmaznak. A hívási eredménykódok szerepelnek a hívási eredmények fájlban az SDK-ban.

Ez a minimális megvalósítás megírható néhány sort. Azonban az éles kódban, tartalmazhat kifinomultabb hibakezelés, az adatbázis további kódot és továbbfejlesztett felhasználói felületet.

### <a name="web-client-code"></a>Webalkalmazás ügyféloldali kódot
Az alábbiakban látható a webes ügyfél kód bemutató lapon.

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
A következő szerveroldali kódot, a multi-factor Authentication konfigurálja és futtassa a 2. lépésben. Standard mód (MODE_STANDARD), amelyhez a felhasználó válaszol a # billentyűt lenyomásával telefonhívást.

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
