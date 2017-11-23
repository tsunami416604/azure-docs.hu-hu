---
title: "Többtényezős hitelesítés software development kit egyéni alkalmazásokba |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan töltse le és engedélyezze a kétlépéses ellenőrzést, az egyéni alkalmazások az Azure MFA SDK segítségével."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: 1c152f67-be02-42a5-a0c7-246fb6b34377
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.openlocfilehash: c9be948a7328b0f535d8683e6cdc695a01654a94
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Building a multi-factor Authentication egyéni alkalmazásokba (SDK)

> [!IMPORTANT]
> A, az Azure multi-factor Authentication Software Development Kit (SDK) rendelkezik lett bejelentéséig. Ez a funkció már nem lesz támogatott új ügyfelek esetén. Az ügyfelek aktuális továbbra is 2018. November 14. amíg a SDK használatával. Az SDK hívások időnek az elteltével sikertelen lesz. 

Az Azure multi-factor Authentication Software Development Kit (SDK) lehetővé teszi a kétlépéses ellenőrzést közvetlenül folyamatokba a bejelentkezés vagy a tranzakció alkalmazásai az Azure AD-bérlő létrehozása.

A multi-factor Authentication SDK C#, Visual Basic (.NET), Java, Perl, PHP és Ruby érhető el. Az SDK-t kínál egy vékony kétlépéses ellenőrzést. Minden kell írnia a kódot, beleértve a megjegyzésként forráskódfájl, például fájlok és a részletes információs fájl tartalmazza. Minden SDK a tanúsítvány és titkos kulcsot, a többtényezős hitelesítési szolgáltató egyedi tranzakciók is tartalmaz. Mindaddig, amíg még meg olyan szolgáltatót, letöltheti az SDK legtöbb nyelveket és formátumban, szükség szerint.

A multi-factor Authentication SDK API-k szerkezete felettébb egyszerű. Győződjön meg a multi-factor Authentication beállítás paraméterek (például a hitelesítési mód) és a felhasználói adatokat (például a telefonszám vagy a PIN-kód érvényesítése) API hívása egy funkcióval. Az API-k lefordítani a felhőalapú Azure multi-factor Authentication szolgáltatást a webes szolgáltatások kérelmek függvény hívása. Az összes hívás tartalmaznia kell egy hivatkozás, amely minden SDK megtalálható titkos tanúsítványra.

Mivel az API-k nincs hozzáférése a felhasználók számára az Azure Active Directoryban regisztrálva, meg kell adni egy fájl vagy az adatbázis felhasználói adatokat. Is az API-k nem tartalmaz regisztrációs vagy a felhasználó felügyeleti funkciókat, ezért kell ezeket a folyamatokat az alkalmazásba.

> [!IMPORTANT]
> Az SDK letöltéséhez létre kell hoznia egy Azure Multi-Factor Auth szolgáltatót, akkor is, ha rendelkezik Azure MFA, AAD Premium vagy EMS licencekkel. Ha erre a célra az Azure többtényezős hitelesítésszolgáltató létrehozása, és már rendelkezik licenccel, ügyeljen arra, hogy a szolgáltató létrehozása a **Per Enabled User** modell. Ezt követően kapcsolja a szolgáltatót az Azure MFA, Azure AD Premium vagy EMS licenceket tartalmazó könyvtárhoz. Ez a konfiguráció biztosítja, hogy Ön csak számlázása Amennyiben több egyedi felhasználók, mint a saját licencek száma a SDK használatával.


## <a name="download-the-sdk"></a>Az SDK letöltése
Az Azure multi-factor Authentication SDK letöltése szükséges egy [Azure többtényezős hitelesítésszolgáltató](multi-factor-authentication-get-started-auth-provider.md).  Ehhez a teljes Azure-előfizetéssel, akkor is, ha az Azure MFA, az Azure AD Premium vagy a nagyvállalati mobilitási csomag licencek a tulajdonosa. Az SDK letöltése nyilvános módszerek decomissioned törölték, mivel az SDK elavult. Ha le kell töltenie az SDK nyisson meg egy támogatási esetet a Microsofttal.

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
