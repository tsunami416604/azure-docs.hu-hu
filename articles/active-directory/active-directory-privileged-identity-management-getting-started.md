<properties
   pageTitle="Ismerkedés az Azure AD Privileged Identity Management alkalmazással | Microsoft Azure"
   description="Megtudhatja, hogyan kezelhet kiemelt identitásokat az Azure portál Azure Active Directory Privileged Identity Management alkalmazásával."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Ismerkedés az Azure AD Privileged Identity Management alkalmazással


Az Azure Active Directory (AD) Privileged Identity Management lehetővé teszi az emelt szintű identitások kezelését, irányítását és felügyeletét, és hozzáférést biztosít az Azure AD és más online szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz.  

Ez a cikk bemutatja, hogyan veheti fel az Azure AD PIM alkalmazást az Azure portál irányítópultjára.

## A Privileged Identity Management alkalmazás felvétele

Az Azure AD Privileged Identity Management használata előtt fel kell vennie az alkalmazást az Azure portál irányítópultjára.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/) a címtára globális rendszergazdájaként.
2. Ha a szervezet több címtárral rendelkezik, akkor kattintson a felhasználónevére az Azure portál jobb felső sarkában, és válassza ki azt a címtárat, amelyben a PIM használatát tervezi.
3. A bal oldali navigációs panelen válassza az **Új** ikont.
4. Válassza a **Biztonság + identitás** elemet.
5. Válassza az **Azure AD Privileged Identity Management** lehetőséget.
6. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.


Ha Ön az első, aki a címtárban az Azure AD Privileged Identity Management alkalmazást használja, akkor a [biztonság varázsló](active-directory-privileged-identity-management-security-wizard.md) végigvezeti a hozzárendelés kezdeti lépésein. Ezután automatikusan Ön lesz a címtár első **Biztonsági rendszergazdája** és **kiemelt szerepkörű rendszergazdája**. Csak a kiemelt szerepkörű rendszergazdák férhetnek hozzá az alkalmazáshoz, hogy más rendszergazdák hozzáférését kezeljék.  

Ha egy másik kiemelt szerepkörű rendszergazda egy vagy több szerepkört rendelt Önhöz, akkor kiválaszthatja, hogy melyik szerepkört aktiválja. Ha Ön is kiemelt szerepkörű rendszergazda, akkor Ön is látni fogja az **Identitások kezelése** elemet.  


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## További lépések

[Az Azure AD Privileged Identity Management áttekintése](active-directory-privileged-identity-management-configure.md) további részleteket tartalmaz arról, hogyan kezelheti a rendszergazdai hozzáférést a szervezetében.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]



<!--HONumber=Jun16_HO2-->


