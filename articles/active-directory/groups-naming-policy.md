---
title: "Az Office 365-csoportok az Azure Active Directoryban (előzetes verzió) neve csoportházirend-beállítások |} Microsoft Docs"
description: "Az Office 365-csoportok az Azure Active Directoryban (előzetes verzió) lejárati beállítása"
services: active-directory
documentationcenter: 
author: curtand
manager: michael.tillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/28/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: cc3ea7f81a924f3f4baa6fd2866c4e552b7c160e
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory-preview"></a>Az Office 365-csoportok az Azure Active Directoryban (előzetes verzió) elnevezési vonatkozó szabályzat alkalmazása

Office 365-csoportok a felhasználók által létrehozott vagy módosított egységes elnevezési szabályai kényszerítéséhez állítsa be a csoportot névadási a bérlők számára az Azure Active Directory (Azure AD). Például az elnevezési házirend segítségével kommunikálnak a függvény egy csoport, a tagság, a földrajzi régióban, vagy ki hozta létre a csoport. A címjegyzék csoportok kategorizálása segítségével is használhat az elnevezési házirendet. A házirendek segítségével a használatban lévő csoporthoz tartozó nevek és aliasok szavak blokkolása.

> [!IMPORTANT]
> Az Office 365 csoportok elnevezési házirend előzetes a használatához az Azure Active Directory Premium P1 licencek minden egyedi felhasználóhoz, amely tagja egy vagy több Office 365-csoportokat.

Az elnevezési házirend létrehozásával vagy szerkesztésével között (például az Outlook, a Microsoft Teams, SharePoint, Exchange vagy Planner) munkaterhelések létrehozott csoportok vonatkozik. A csoport nevét és a csoport alias alkalmazva. Ha az Azure ad-ben a elnevezési házirend beállításához és névadási meglévő Exchange csoport, az Azure AD elnevezési házirend érvényesül.

## <a name="naming-policy-features"></a>Elnevezési házirend szolgáltatások
Az Office 365-csoportok névadási kényszerítheti a két különböző módon:

-   **Előtag-utótag névadási** előtagok vagy utótagokat, majd automatikusan hozzáadódnak a csoportok elnevezési kényszerítése adhatja meg (például a csoport nevét a "csoport\_japán\_saját csoport\_ Mérnöki", csoport\_japán\_ az előtag és \_mérnöki pedig utótag). 

-   **Egyéni blokkolva szavak** tiltott szót adott készlete feltöltheti a szervezetben lévő felhasználók (például "CEO, a bérszámfejtési, HR") által létrehozott csoportok letiltása.

### <a name="prefix-suffix-naming-policy"></a>Előtag-utótag elnevezési házirend

Az elnevezési általános szerkezetét "Előtag [GroupName] utótag". Amíg több előtagok és utótagok megadásához csak rendelkezhet egy példányát a [GroupName] beállításban. Az előtagok vagy utótagok lehet rögzített karakterláncok vagy felhasználói attribútumok például \[részleg\] , amely a csoport létrehozó felhasználó alapján van helyére. A teljes megengedett karakterek számát a előtag és az utótag-karakterlánc kombinált 53 karakterek. 

Előtagok és utótagok csoport nevét és a csoport alias által támogatott különleges karaktereket tartalmazhat. Az összes karaktert a előtag vagy utótag által nem támogatott a csoport alias továbbra is alkalmazza a csoport nevét, de a csoport alias eltávolítja. Ez a korlátozás miatt a előtagok és a csoport nevét alkalmazott utótagok lehet a csoport alias alkalmazott eltérő. 

#### <a name="fixed-strings"></a>Rögzített karakterláncok

Karakterláncok segítségével könnyebben vizsgálja, és megkülönböztetni azokat a csoportokat a globális címlista és a bal oldali navigációs hivatkozások csoport munkaterhelések. A közös előtagok némelyike kulcsszavak például a "csoport\_neve", "\#neve","\_neve"

#### <a name="user-attributes"></a>Felhasználói attribútumok

Attribútumok, amelyek segítségével is használhat, és a felhasználók azonosítására, melyik részleg, office vagy földrajzi régióban, amelynek a csoportot létrehozták. Például, ha adhat meg, a névadási `PrefixSuffixNamingRequirement = “GRP [GroupName] [Department]”`, és `User’s department = Engineering`, akkor lehet, hogy az érvényben lévő csoport neve "Csoport saját csoport mérnöki csapathoz." Támogatja az Azure AD-attribútumok \[részleg\], \[vállalati\], \[Office\], \[StateOrProvince\], \[CountryOrRegion \], \[Cím\]. Nem támogatott felhasználói attribútumok tekintendők rögzített karakterláncok; például "\[irányítószám\]". Egyéni oszlopainál és a bővítmény nem támogatottak.

Azt javasoljuk, hogy használja-e attribútumok, amelyek a szervezet összes felhasználója számára kitöltött értékeket, és ne attribútumokat, amelyek hosszú értéket használjon.

### <a name="custom-blocked-words"></a>Egyéni letiltott szavakat

Letiltott szólista a csoporthoz tartozó nevek és aliasok letiltása kifejezések vesszővel tagolt listáját. Nincs karakterláncrészletet keresések megy végbe. A csoport nevét, és egy vagy több egyéni letiltott szavakat közötti pontos egyezést hibát kiváltó szükséges. Részleges karakterlánc, hogy a felhasználók használhatják a gyakori szavakat, például a "Class", akkor is, ha "osztály" tiltott szót nem végrehajtja.

Letiltott word lista szabályok:
- Letiltott szavak nincsenek kis-és nagybetűket.
- Amikor a felhasználó letiltott szót adja meg egy csoportnevet részeként, tiltott szót tartalmazó hibaüzenetet látják.
- Nincsenek letiltott szavak karakter korlátozások.
- Nincs 5000 kifejezések letiltott szavak listájához konfigurált felső korlátja. 

### <a name="administrator-override"></a>Rendszergazdai felülbírálása

Kijelölt rendszergazdák is alól ezek a házirendek összes csoport munkaterhelések és végpontok, hogy a csoportok letiltott szavak használatával hozhatnak létre, és a saját elnevezési konvenciókat. Az alábbiakban a csoport elnevezési házirend alól rendszergazdai szerepkörök listáját.

- Globális rendszergazda
- Partner 1. rétegbeli támogatása
- 2. szintű támogatási partner
- Felhasználói fiók rendszergazdája
- Címtárírók

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>Telepítse a PowerShell-parancsmagok segítségével elnevezési házirend konfigurálása

Ügyeljen arra, hogy távolítsa el az Azure Active Directory PowerShell bármely korábbi verziója Graph modul Windows PowerShell és telepítse [Azure Active Directory PowerShell diagramhoz – a nyilvános előzetes 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) futtatása előtt a PowerShell-parancsokat. 

1. Nyissa meg a Windows PowerShell alkalmazást rendszergazdaként.
2. Távolítsa el a AzureADPreview minden korábbi verzióját.
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. Telepítse a legújabb verzióját AzureADPreview.
  
  ````
  Install-Module AzureADPreview
  ````
Ha egy nem megbízható tárház elérése kéri, írja be a **Y**. Új modul telepítése néhány percig is eltarthat.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Házirend neve a bérlő az Azure AD PowerShell csoport konfigurálása

1. Nyisson meg egy Windows PowerShell-ablakot a számítógépen. Emelt szintű jogosultságok nélkül úgy is megnyithatja.

2. A következő parancsokat a parancsmagok futtatásához.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  Az a **jelentkezzen be a fiókba** a megnyíló képernyőn írja be a rendszergazdai fiókot és jelszót kapcsolódik a szolgáltatáshoz, és válassza ki **bejelentkezés**.

3. Kövesse a [csoport beállításainak konfigurálása az Azure Active Directory parancsmagok](active-directory-accessmanagement-groups-settings-cmdlets.md) ehhez a bérlőhöz tartozó beállítások létrehozásához.

### <a name="view-the-current-settings"></a>A jelenlegi beállítások megtekintéséhez

1. A jelenlegi beállítások megtekintéséhez az aktuális elnevezési házirend beolvasása.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Jelenítse meg az aktuális csoport beállításait.
  
  ````
  $Setting.Values
  ````
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Az elnevezési házirend és az egyéni letiltott szavakat

1. Az Azure AD PowerShell segítségével állítsa be a csoport előtagok és utótagokat.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Állítsa be a korlátozni kívánt egyéni letiltott szavakat. A következő példa bemutatja, hogyan adhat hozzá a saját egyéni szavakat.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Hatékony, többek között az alábbi példában az új házirend a beállítások mentéséhez.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
Ennyi az egész. Hogy a elnevezési szabályzatot, és a blokkolt szavakat hozzá.

## <a name="export-or-import-the-list-of-custom-blocked-words"></a>Exportál vagy importál egyéni letiltott szavak listája

További információkért lásd: a cikk [csoport beállításainak konfigurálása az Azure Active Directory parancsmagok](active-directory-accessmanagement-groups-settings-cmdlets.md).

Íme egy példa egy PowerShell-parancsprogram több tiltott szót exportálása:

````
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
````

Íme egy példa a PowerShell-parancsprogram több tiltott szót importálása:

````
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
$Settings["EnableMSStandardBlockedWords"] = $True
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
````

## <a name="naming-policy-experiences-across-office-365-apps"></a>Office 365-alkalmazások között elnevezési házirend lép

Miután beállította az elnevezési csoportházirend az Azure AD, ha egy felhasználó egy csoportot hoz létre az Office 365-alkalmazásra, láthatják: 

* A tekintse meg a név (az előtagok és utótagok) a elnevezési házirendjének megfelelően, amint a felhasználó beírja a csoport neve
* Ha a felhasználó letiltott szavak ad, azok üzenet jelenik meg egy hiba, eltávolíthatják a letiltott szavakat.

Számítási feladat | Megfelelőség
----------- | -------------------------------
Az Azure Active Directory portálon | Az Azure AD és a hozzáférési Panel portálhoz a elnevezési kényszerített házirend nevének megjelenítése, ha a felhasználó létrehozásakor vagy szerkesztésekor a csoport a csoport nevét. Amikor a felhasználó adja meg egy egyéni tiltott szót, úgy, hogy a felhasználó el is távolíthatja tiltott szót tartalmazó hibaüzenet jelenik meg.
Outlook Web Access (OWA) | Az Outlook Web Access jeleníti meg az elnevezési házirend neve lépnek érvénybe, amikor a felhasználó egy csoport nevét vagy a csoport alias. Amikor egy felhasználó egy egyéni tiltott szót adja meg, egy hibaüzenet jelenik meg a felhasználói felület és a tiltott szót, hogy a felhasználó el is távolíthatja.
Az asztali Outlook | Az asztali Outlook-ben létrehozott csoportok megfelelőek a elnevezési házirend-beállításokkal. Asztali Outlook alkalmazás még nem jeleníti meg az előzetes verzióját a kényszerített csoport nevét, és az egyéni tiltott szót hibák nem ad vissza, ha a felhasználó megadja a csoport nevét. Azonban a elnevezési házirend létrehozásakor vagy szerkesztésekor a csoport automatikusan érvényes, és a felhasználók hibaüzenetek megtekintéséhez, ha a csoport nevét vagy aliasát egyéni letiltott szavakat.
Microsoft Teams | Microsoft Teams jeleníti meg a csoport elnevezési házirendnév lépnek érvénybe, amikor a felhasználó megadja a csoport nevét. Amikor a felhasználó adja meg egy egyéni tiltott szót, egy hibaüzenet jelenik meg és a tiltott szót, hogy a felhasználó el is távolíthatja.
SharePoint  |  SharePoint elnevezési kényszerített házirend nevét jeleníti meg, amikor a felhasználó által a hely neve vagy e-mail cím csoportban. Amikor egy felhasználó ír be egy egyéni tiltott szót, egy hibaüzenet jelenik meg, és a tiltott szót, hogy a felhasználó el is távolíthatja.
Microsoft Stream | Microsoft Stream jeleníti meg a csoport elnevezési házirendnév lépnek érvénybe, amikor a felhasználó egy csoport nevét vagy a csoport e-mail aliasát. Amikor egy felhasználó egy egyéni tiltott szót adja meg, egy hibaüzenet jelenik meg tiltott szót tartalmazó, a felhasználó el is távolíthatja.
Outlook iOS és Android-alkalmazás | Az Outlook-alkalmazásokban létrehozott csoportok a szabályzatnak megfelelő konfigurált elnevezési. Outlook mobile Apps még nem jeleníti meg az előzetes verzióját a elnevezési kényszerített házirend nevét, és egyéni tiltott szót hibák nem ad vissza, ha a felhasználó megadja a csoport nevét. Azonban az elnevezési házirend automatikusan alkalmazza az kattint létrehozása/szerkesztése, és a felhasználók hibaüzenetek megtekintéséhez, ha a csoport nevét vagy aliasát egyéni letiltott szavakat.
Csoportok mobilalkalmazás | A csoportok mobilalkalmazásban létrehozott csoportok a szabályzatnak megfelelő elnevezési. Csoportok mobilalkalmazás nem jeleníti meg az előzetes verzióját a névadási, és nem ad vissza egyéni tiltott szót hibák, amikor a felhasználó megadja a csoport nevét. De a elnevezési házirend létrehozásakor vagy szerkesztésekor a csoport automatikusan érvényes, és a felhasználók számára jelenik meg a megfelelő hibákkal egyéni tiltott szót a csoport nevét vagy aliasát esetén.
Planner | A névadási Planner megfelel. Planner elnevezési házirend előzetes jeleníti meg, a csomag nevét beírásához. Amikor a felhasználó adja meg egy egyéni tiltott szót, egy hibaüzenet jelenik meg a csomag létrehozásakor.
Az ügyfél Engagement Dynamics 365 | Az ügyfél Engagement Dynamics 365 megfelel az elnevezési házirendet. Dynamics 365 elnevezési kényszerített házirend nevét jeleníti meg, amikor a felhasználó egy csoport nevét vagy a csoport e-mail aliasát. Amikor a felhasználó adja meg egy egyéni tiltott szót, egy hibaüzenet jelenik meg tiltott szót tartalmazó, a felhasználó el is távolíthatja.
School Data Sync (SDS) | SDS keresztül létrehozott csoportok elnevezési házirend felel meg, de a névadási nem automatikusan alkalmazza. SDS rendszergazdáknak kell az előtagok és utótagok hozzáfűzése osztályneveket, amelynek csoportok kell létrehozni és SDS majd feltölteni. Csoport létrehozása vagy szerkesztése más módon fognak működni.
Outlook ügyfél Manager (OCM) | Outlook ügyfél Manager megfelel az elnevezési házirendet, amely a rendszer automatikusan alkalmaz az Outlook ügyfél kezelőt hozott létre a csoport. Ha egy egyéni tiltott szót észlel, csoport létrehozása a OCM le van tiltva, és a felhasználó le van tiltva a OCM alkalmazással.
Számítógéptermi alkalmazás | Számítógéptermi alkalmazásban létrehozott csoportok felel meg az elnevezési szabályzatának, de elnevezési nincs érvényben automatikusan, és az elnevezési házirend előzetes nem jelennek meg a felhasználók egy osztályteremben csoportnév beírása közben. Felhasználók meg kell adnia az előtagok és utótagok kényszerített osztályteremben csoport nevét. Ha nem, akkor a osztályteremben csoport létrehozása vagy szerkesztése hibák művelet meghiúsul.
Power BI | A Power BI munkaterületek érhetők a szabályzatnak megfelelő elnevezési.    
Yammer | Yammer a csatlakoztatott csoportok nem a konfigurált elnevezési házirendek kikényszerítéséhez. A naming házirenddel rendelkező szervezetek számára Yammer csatlakozó nem Office 365 csoportok, amelyek nem felelnek meg a névadási örökölt Yammer-csoportokat hoz létre.
StaffHub  | StaffHub csapatok ne hajtsa végre az elnevezési házirendet, azonban az alapul szolgáló Office 365-csoport nem. StaffHub csoport neve nem érvényes az előtagok és utótagot, és nem ellenőrzi az egyéni tiltott szót. De StaffHub nem alkalmazza az előtagok és utótagok és a blokkolt szavak eltávolítja az alapul szolgáló Office 365-csoport.
Exchange PowerShell | Exchange PowerShell-parancsmagok az alábbiak a szabályzatnak megfelelő elnevezési. Felhasználók hibaüzenetek megfelelő javasolt előtagok és utótagok és egyéni tiltott szavakra ha azok nem követi a névadási a csoport nevét és a csoport alias (mailNickname).
Az Azure Active Directory PowerShell-parancsmagok | Az Azure Active Directory PowerShell-parancsmagok kompatibilisek az elnevezési házirend. Felhasználók hibaüzenetek megfelelő javasolt előtagok és utótagok és egyéni tiltott szavakra ha azok nem követi a csoporthoz tartozó nevek és csoport alias elnevezési konvenciót.
Az Exchange felügyeleti központban | Az Exchange felügyeleti központban megfelel elnevezési házirend. Felhasználók hibaüzenetek megfelelő javasolt előtagok és utótagok és egyéni tiltott szavakra ha azok nem követi a csoport nevét és a csoport alias elnevezési konvenciót.
Office 365 felügyeleti központ | Office 365 felügyeleti központ megfelel elnevezési házirend. Amikor egy felhasználó hoz létre vagy módosításokat a csoportneveket, a rendszer automatikusan alkalmazza a elnevezési házirendet, és felhasználók fogadják a hibákat, ha azok adjon meg egyéni tiltott szót. Az Office 365 felügyeleti központ még nem jeleníti meg a névadási előnézetét, és egyéni tiltott szót hibák nem ad vissza, ha a felhasználó megadja a csoport nevét.

## <a name="next-steps"></a>További lépések
Ezek a cikkek kiegészítő információt nyújt az Azure Active Directory csoportokat.

* [Tekintse meg a meglévő csoportok](active-directory-groups-view-azure-portal.md)
* [Office 365-csoportok-elévülési szabályzatának](active-directory-groups-lifecycle-azure-portal.md)
* [Egy csoport beállításainak kezelése](active-directory-groups-settings-azure-portal.md)
* [A csoport tagjai kezelése](active-directory-groups-members-azure-portal.md)
* [A csoport tagságát kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoport dinamikus szabályok kezelése](active-directory-groups-dynamic-membership-azure-portal.md)
