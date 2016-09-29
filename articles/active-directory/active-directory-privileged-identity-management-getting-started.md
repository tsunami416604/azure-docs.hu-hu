<properties
   pageTitle="Ismerkedés az Azure AD Privileged Identity Management alkalmazással | Microsoft Azure"
   description="Megtudhatja, hogyan kezelhet kiemelt identitásokat az Azure Portal Azure Active Directory Privileged Identity Management alkalmazásával."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="kgremban"/>


# Ismerkedés az Azure AD Privileged Identity Management alkalmazással


Az Azure Active Directory (AD) Privileged Identity Management segítségével kezelheti, irányíthatja és felügyelheti a szervezeten belüli hozzáféréseket. Ebbe beletartozik az Azure AD és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

Ez a cikk bemutatja, hogyan veheti fel az Azure AD PIM alkalmazást az Azure Portal irányítópultjára.

## A Privileged Identity Management alkalmazás felvétele

Az Azure AD Privileged Identity Management használata előtt fel kell vennie az alkalmazást az Azure Portal irányítópultjára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a címtára globális rendszergazdájaként.
2. Ha a szervezet több címtárral rendelkezik, akkor kattintson a felhasználónevére az Azure Portal jobb felső sarkában. Válassza ki azt a címtárat, amelyben a PIM használatát tervezi.
3. Válassza a **További szolgáltatások** lehetőséget, és a Szűrők szövegmezővel keresse meg az **Azure AD Privileged Identity Management** alkalmazást.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.


Ha Ön az első, aki a címtárban az Azure AD Privileged Identity Management alkalmazást használja, akkor a [biztonság varázsló](active-directory-privileged-identity-management-security-wizard.md) végigvezeti a hozzárendelés kezdeti lépésein. Ezután automatikusan Ön lesz a címtár első **biztonsági rendszergazdája** és **kiemelt szerepkörű rendszergazdája**. Csak a kiemelt szerepkörű rendszergazdák férhetnek hozzá az alkalmazáshoz, hogy más rendszergazdák hozzáférését kezeljék.  

## A feladatok megkeresése

Az Azure AD Privileged Identity Management beállítása után az alkalmazás minden megnyitásakor megjelenik a navigációs panel. Ezen a panelen végezheti el az identitáskezelési feladatokat.

![Felső szintű PIM-tevékenységek – képernyőkép](./media/active-directory-privileged-identity-management-getting-started/pim_tasks.png)

- A **Saját szerepkörök aktiválása** lehetőség kiválasztással megnyithatja az Önhöz rendelt szerepkörök listáját. Itt aktiválhatja az Ön számára elérhető szerepköröket.

- A **Kiemelt szerepkörök kezelése** az az irányítópult, amelyen a kiemelt szerepkörök rendszergazdái kezelhetik a szerepkör-hozzárendeléseket, módosíthatják a szerepkör-aktiválási beállításokat, elindíthatják a hozzáférési felülvizsgálatokat stb. Az irányítópulton található beállítások kizárólag a kiemelt szerepkörű rendszergazdák számára érhetők el.

- Az **Emelt szintű hozzáférés felülvizsgálata** lehetőséggel megtekintheti a függőben lévő hozzáférési felülvizsgálatokat, amelyeket be kell fejeznie, legyen szó akár a saját hozzáféréseiről vagy egy másik felhasználóéról. 


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Következő lépések

[Az Azure AD Privileged Identity Management áttekintése](active-directory-privileged-identity-management-configure.md) további részleteket tartalmaz arról, hogyan kezelheti a rendszergazdai hozzáférést a szervezetében.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png



<!--HONumber=Sep16_HO4-->


