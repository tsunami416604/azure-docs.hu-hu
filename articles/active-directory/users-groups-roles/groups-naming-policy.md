---
title: Csoportelnevezési házirend kényszerítése az Azure Active Directoryban | Microsoft dokumentumok
description: Az Office 365-csoportok elnevezési házirendjének beállítása az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 141e83e21db18f21468113fd9927c2bdd2ed176d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497877"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Elnevezési házirend kényszerítése az Azure Active Directoryban az Office 365-csoportokon

A felhasználók által létrehozott vagy szerkesztett Office 365-csoportok konzisztens elnevezési konvencióinak kényszerítéséhez állítson be egy csoportelnevezési szabályzatot a bérlők számára az Azure Active Directoryban (Azure AD). Az elnevezési házirend segítségével például kommunikálhatja egy csoport, a tagság, a földrajzi régió vagy a csoport létrehozójának funkcióját. Az elnevezési házirend segítségével kategorizálhatja a csoportokat a címjegyzékben. A házirend segítségével letilthatja bizonyos szavak csoportnevekben és aliasokban való használatát.

> [!IMPORTANT]
> Az Azure AD-névhasználati szabályzat használata az Office 365-csoportokhoz megköveteli, hogy rendelkezik, de nem feltétlenül rendel azure Active Directory prémium szintű P1-licenccel vagy Azure AD Basic EDU licenccel minden olyan egyedi felhasználóhoz, aki egy vagy több Office 365-csoport tagja.

Az elnevezési szabályzat a különböző munkaterhelések (például outlook, Microsoft Teams, SharePoint, Exchange vagy Planner) között létrehozott csoportok létrehozására vagy szerkesztésére vonatkozik. A program a csoportnévre és a csoportaliasra is vonatkozik. Ha beállítja az elnevezési szabályzatot az Azure AD-ben, és rendelkezik egy meglévő Exchange-csoportelnevezési szabályzattal, az Azure AD-névhasználati szabályzat a szervezetben lesz kényszerítve.

A csoportelnevezési házirend konfigurálásakor a rendszer a végfelhasználók által létrehozott új Office 365-csoportokra alkalmazza a házirendet. Az elnevezési házirend nem vonatkozik bizonyos címtárszerepkörökre, például a globális rendszergazda vagy a felhasználói rendszergazda (lásd alább a csoportelnevezési házirend alól mentesített szerepkörök teljes listáját). Meglévő Office 365-csoportok esetén a házirend nem lesz azonnal érvényes a konfiguráció időpontjában. Miután a csoport tulajdonosa szerkéseli a csoportok csoportnevét, az elnevezési házirend érvénybe lép.

## <a name="naming-policy-features"></a>A házirend szolgáltatásainak elnevezése

A csoportok elnevezési házirendje két különböző módon kényszeríthető ki:

- **Előtag-utótag elnevezési házirend** Olyan előtagokat vagy utótagokat definiálhat, amelyeket aztán automatikusan hozzáad a csoportok elnevezési konvenciójának érvényesítéséhez (például\_ a "GRP \_\_JAPAN\_My Group\_Engineering" csoportnévben, a GRP\_JAPAN az előtag, a Mérnöki pedig az utótag). 

- **Egyéni letiltott szavak** Feltöltheti a szervezetre jellemző letiltott szavakkészletét, amelyeket a felhasználók által létrehozott csoportokban (például "CEO, Payroll, HR") tiltanak le.

### <a name="prefix-suffix-naming-policy"></a>Előtag-utótag elnevezési házirend

Az elnevezési konvenció általános szerkezete az "Előtag[GroupName]Utótag". Bár több előtagot és utótagot is definiálhat, a beállításban csak a [GroupName] egy példánya szerepelhet. Az előtagok vagy utótagok lehetnek rögzített karakterláncok vagy \[felhasználói\] attribútumok, például a csoportot létrehozó felhasználó alapján helyettesített Részleg. Az előtag és utótagkarakterláncok összesen megengedett karakterei összesen 53 karakterből állnak. 

Az előtagok és utótagok olyan speciális karaktereket tartalmazhatnak, amelyeket a csoportnév és a csoportalias támogat. Az előtagban vagy utótagban lévő olyan karakterek, amelyek nem támogatottak a csoportaliasban, továbbra is érvényesek a csoport nevében, de törlődnek a csoportaliasból. E korlátozás miatt a csoportnévre alkalmazott előtagok és utótagok eltérhetnek a csoportaliasra alkalmazott előtagoktól. 

#### <a name="fixed-strings"></a>Rögzített karakterláncok

A karakterláncok segítségével megkönnyítheti a csoportok bekéselését és megkülönböztetését a globális címlistában és a csoportmunkaterhelések bal oldali navigációs hivatkozásaiban. A gyakori előtagok közül néhány olyan kulcsszó, mint a "Grp\_name", "Name",\#"Name"\_

#### <a name="user-attributes"></a>Felhasználói attribútumok

Olyan attribútumokat használhat, amelyek segítségével Ön és a felhasználók azonosíthatják, hogy melyik részleghez, irodához vagy földrajzi régióhoz hozták létre a csoportot. Ha például az elnevezési `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`házirendet `User’s department = Engineering`a - és a , majd egy kényszerített csoportnév lehet "GRP My Group Engineering" ( GRP Csoporttervezés) A támogatott Azure \[AD attribútumok a következők: Részleg\], \[Vállalat,\]Hivatal, \[\] \[Államvagy Tartomány\], \[CountryOrRegion\], \[Cím\]. A nem támogatott felhasználói attribútumokat a rendszer rögzített karakterláncként kezeli; például "\[postalCode\]". A bővítményattribútumok és az egyéni attribútumok nem támogatottak.

Azt javasoljuk, hogy olyan attribútumokat használjon, amelyek értékei a szervezet összes felhasználója számára ki vannak töltve, és ne használjanak hosszú értékekkel rendelkező attribútumokat.

### <a name="custom-blocked-words"></a>Egyéni letiltott szavak

A letiltott szólista a csoportnevekben és aliasokban letiltandó kifejezések vesszővel tagolt listája. A rendszer nem hajt végre karakterlánc-keresést. A hiba kiváltásához pontos egyezés szükséges a csoportnév és egy vagy több egyéni letiltott szó között. Az alkarakterlánc-keresés nem történik meg, így a felhasználók akkor is használhatnak olyan gyakori szavakat, mint az "Osztály", még akkor is, ha a "lass" egy blokkolt szó.

Blokkolt szólista szabályok:

- A letiltott szavak nem érzékenyek a kis- és nagybetűkre.
- Amikor egy felhasználó egy letiltott szót ad meg egy csoportnév részeként, hibaüzenet jelenik meg a letiltott szóval.
- A letiltott szavakra nincsenek karakterkorlátozások.
- Van egy felső határa 5000 mondat, hogy be lehet állítani a blokkolt szavak listáját. 

### <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Az elnevezési házirend konfigurálásához az egyik folowing szerepkör szükséges:
- Globális rendszergazda
- Csoportadminisztrátor
- Rendszergazda

A kijelölt rendszergazdák mentesülhetnek ezek a házirendek alól az összes csoportszámítási feladat és végpont között, így letiltott szavakkal és saját elnevezési konvenciókkal hozhatnak létre csoportokat. Az alábbiakban a csoportelnevezési házirend alól mentesített rendszergazdai szerepkörök listája szerepel.

- Globális rendszergazda
- Partner szintű 1.
- Partner2 szintű támogatás
- Rendszergazda
- Címtár írók

## <a name="configure-naming-policy-in-azure-portal"></a>Elnevezési házirend konfigurálása az Azure Portalon

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy csoportrendszergazdai fiókkal.
1. Válassza **a Csoportok**lehetőséget, majd az **Elnevezési házirend** lehetőséget választva nyissa meg az Elnevezési házirend lapot.

    ![az Elnevezési házirend lap megnyitása a felügyeleti központban](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Az előtag-utótag elnevezési házirendjének megtekintése vagy szerkesztése

1. Az **Elnevezési házirend** lapon válassza a **Csoportelnevezési házirend**lehetőséget.
1. Az aktuális előtagot vagy utótag-elnevezési házirendeket egyenként is megtekintheti vagy szerkesztheti, ha kiválasztja az elnevezési házirend részeként érvényesíteni kívánt attribútumokat vagy karakterláncokat.
1. Ha el szeretne távolítani egy előtagot vagy utótagot a listából, jelölje ki az előtagot vagy utótagot, majd kattintson a **Törlés gombra.** Egyszerre több elem is törölhető.
1. Mentse a módosításokat az új házirend életbe léptetéséhez a **Mentés**gombra kattintva.

### <a name="edit-custom-blocked-words"></a>Egyéni letiltott szavak szerkesztése

1. Az **Elnevezési házirend** lapon válassza a **Letiltott szavak**lehetőséget.

    ![letiltott szavak listájának szerkesztése és feltöltése az elnevezési házirendhez](./media/groups-naming-policy/blockedwords.png)

1. A **Letöltés**gombra kattintva megtekintheti vagy szerkesztheti az egyéni blokkolt szavak aktuális listáját.
1. Töltse fel az egyéni blokkolt szavak új listáját a fájl ikonjának kiválasztásával.
1. Mentse a módosításokat az új házirend életbe léptetéséhez a **Mentés**gombra kattintva.

## <a name="install-powershell-cmdlets"></a>PowerShell-parancsmagok telepítése

A PowerShell-parancsok futtatása előtt mindenképpen távolítsa el a Windows PowerShellhez készült Azure Active Directory PowerShell for Graph modul összes régebbi verzióját, és telepítse az [Azure Active Directory PowerShell for Graph 2.0.0.137-es nyilvános előzetes kiadását](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Nyissa meg a Windows PowerShell alkalmazást rendszergazdaként.
2. Távolítsa el az AzureADPreview korábbi verzióit.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Telepítse az AzureADPreview legújabb verzióját.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Ha a rendszer rákérdez egy nem megbízható tárház elérésére, írja be az **Y**értéket. Az új modul telepítése néhány percet is igénybe vehet.

## <a name="configure-naming-policy-in-powershell"></a>Elnevezési házirend konfigurálása a PowerShellben

1. Nyisson meg egy Windows PowerShell-ablakot a számítógépen. Megnyithatja emelt szintű jogosultságok nélkül.

1. Futtassa a következő parancsokat a parancsmagok futtatásának előkészítéséhez.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   A megjelenő **Bejelentkezés a fiókba** párbeszédpanelen adja meg a rendszergazdai fiókot és jelszót, hogy kapcsolatot létesítsen a szolgáltatással, majd válassza a **Bejelentkezés** lehetőséget.

1. Kövesse az [Azure Active Directory-parancsmagok a csoportbeállítások konfigurálásához](groups-settings-cmdlets.md) című rész lépéseit a bérlő csoportbeállításainak létrehozásához.

### <a name="view-the-current-settings"></a>Az aktuális beállítások megtekintése

1. Az aktuális beállítások megtekintéséhez olvassa be az aktuális elnevezési házirendet.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Jelenítse meg az aktuális csoportbeállításokat.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Az elnevezési házirend és az egyéni letiltott szavak beállítása

1. Állítsa be a csoportnév előtagjait és utótagjait az Azure AD PowerShellben. A szolgáltatás megfelelő működéséhez a [GroupName] beállítást be kell építeni a beállításba.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Állítsa be az egyéni letiltott szavakat, amelyek használatát korlátozni szeretné. A következő példa szemlélteti, hogyan adhatja hozzá saját egyéni szavait.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Mentse az új házirend beállításait, például a következő példában.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Ennyi az egész. Beállította az elnevezési házirendet, és hozzáadta a letiltott szavakat.

## <a name="export-or-import-custom-blocked-words"></a>Egyéni letiltott szavak exportálása vagy importálása

További információt az [Azure Active Directory-parancsmagok a csoportbeállítások konfigurálásáról szóló](groups-settings-cmdlets.md)cikkben talál.

Íme egy példa egy PowerShell-parancsfájlra, amely több letiltott szót exportál:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Íme egy példa powershell-parancsfájl több letiltott szó importálására:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Az elnevezési házirend eltávolítása

### <a name="remove-the-naming-policy-using-azure-portal"></a>Az elnevezési szabályzat eltávolítása az Azure Portal használatával

1. Az **Elnevezési házirend** lapon válassza a **Házirend törlése**lehetőséget.
1. A törlés megerősítése után az elnevezési házirend törlődik, beleértve az összes előtag-utótag elnevezési házirendet és az egyéni letiltott szavakat.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Az elnevezési szabályzat eltávolítása az Azure AD PowerShell használatával

1. Ürítse ki a csoportnév-előtagokat és utótagokat az Azure AD PowerShellben.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Ürítse ki az egyéni blokkolt szavakat.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Mentse a beállításokat.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Tapasztalás az Office 365-alkalmazásokban

Miután beállított egy csoportelnevezési szabályzatot az Azure AD-ben, amikor egy felhasználó létrehoz egy csoportot egy Office 365-alkalmazásban, a következőket látják:

- A név előnézete az elnevezési házirendnek megfelelően (előtagokkal és utótagokkal), amint a felhasználó beírja a csoport nevét
- Ha a felhasználó letiltott szavakat ad meg, hibaüzenet jelenik meg, hogy eltávolíthassa a letiltott szavakat.

Számítási feladat | Megfelelőség
----------- | -------------------------------
Azure Active Directory-portálok | Az Azure AD portál és a Hozzáférési panel portál on the naming policy enforced name when the user types in a group name when creating or editing a group. Amikor egy felhasználó egyéni blokkolt szót ad meg, egy hibaüzenet jelenik meg a letiltott szóval, hogy a felhasználó eltávolíthassa azt.
Outlook Web Access (OWA) | Az Outlook Web Access akkor jeleníti meg az elnevezési házirend kényszerített nevét, amikor a felhasználó csoportnevet vagy csoportaliast ad meg. Amikor egy felhasználó egyéni blokkolt szót ad meg, egy hibaüzenet jelenik meg a felhasználói felületen a letiltott szóval együtt, hogy a felhasználó eltávolíthassa azt.
Az Outlook Asztal | Az Asztali Outlook ban létrehozott csoportok megfelelnek az elnevezési házirend beállításainak. Az asztali Outlook alkalmazás még nem jeleníti meg az kényszerített csoportnév előnézetét, és nem adja vissza az egyéni blokkolt szóhibákat, amikor a felhasználó beírja a csoport nevét. Az elnevezési házirend azonban automatikusan alkalmazásra kerül egy csoport létrehozásakor vagy szerkesztésekor, és a felhasználók hibaüzeneteket látnak, ha a csoport nevében vagy aliasában egyéni letiltott szavak vannak.
Microsoft Teams | A Microsoft Teams megjeleníti a csoportelnevezési házirend kényszerített nevét, amikor a felhasználó megadja a csoport nevét. Amikor egy felhasználó egyéni letiltott szót ad meg, egy hibaüzenet jelenik meg a letiltott szóval együtt, hogy a felhasználó eltávolíthassa azt.
SharePoint  |  A SharePoint akkor jeleníti meg az elnevezési házirend kényszerített nevét, amikor a felhasználó megírja a webhely nevét vagy csoport e-mail címét. Amikor egy felhasználó egyéni blokkolt szót ad meg, egy hibaüzenet jelenik meg a letiltott szóval együtt, hogy a felhasználó eltávolíthassa azt.
Microsoft Stream | A Microsoft Stream akkor jeleníti meg a csoportelnevezési házirend kényszerített nevét, amikor a felhasználó csoportnevet vagy csoporte-mail aliast ad meg. Amikor egy felhasználó egyéni blokkolt szót ad meg, egy hibaüzenet jelenik meg a letiltott szóval, hogy a felhasználó eltávolíthassa azt.
Outlook iOS és Android alkalmazás | Az Outlook-alkalmazásokban létrehozott csoportok megfelelnek a konfigurált elnevezési szabályzatnak. Az Outlook mobilalkalmazás még nem jeleníti meg az elnevezési házirend kényszerített nevének előnézetét, és nem ad vissza egyéni letiltott szóhibákat, amikor a felhasználó beírja a csoport nevét. Az elnevezési házirend azonban automatikusan a Create/edit gombra kattintva kerül alkalmazásra, és a felhasználók hibaüzeneteket látnak, ha a csoport nevében vagy aliasában egyéni letiltott szavak vannak.
Csoportok mobilalkalmazás | A Csoportok mobilalkalmazásban létrehozott csoportok megfelelnek az elnevezési szabályzatnak. A Csoportok mobilalkalmazás nem jeleníti meg az elnevezési házirend előnézetét, és nem ad vissza egyéni blokkolt szóhibákat, amikor a felhasználó beírja a csoport nevét. De az elnevezési házirend automatikusan alkalmazva van egy csoport létrehozásakor vagy szerkesztésekor, és a felhasználók megfelelő hibákat kapnak, ha egyéni letiltott szavak vannak a csoport nevében vagy aliasában.
Planner | A Planner megfelel az elnevezési szabályzatnak. A Planner megjeleníti az elnevezési szabályzat előnézetét a terv nevének beírásakor. Amikor egy felhasználó egyéni letiltott szót ad meg, hibaüzenet jelenik meg a terv létrehozásakor.
Dynamics 365 for Customer Engagement | A Dynamics 365 for Customer Engagement megfelel az elnevezési szabályzatnak. A Dynamics 365 az elnevezési házirend kényszerített nevét jeleníti meg, amikor a felhasználó csoportnevet vagy csoporte-mail aliast ad meg. Amikor a felhasználó egyéni blokkolt szót ad meg, egy hibaüzenet jelenik meg a letiltott szóval, hogy a felhasználó eltávolíthassa azt.
Iskolai adatszinkronizálás (SDS) | Az SDS-en keresztül létrehozott csoportok megfelelnek az elnevezési házirendnek, de az elnevezési szabályzat nem lesz automatikusan alkalmazva. Az SDS-rendszergazdáknak hozzá kell fűzniük az előtagokat és utótagokat azokhoz az osztálynevekhez, amelyekhez csoportokat kell létrehozni, majd feltölteni az SDS-be. A csoport létrehozása vagy szerkesztése egyébként sikertelen lenne.
Outlook Ügyfélkezelő (OCM) | Az Outlook Customer Manager megfelel az elnevezési házirendnek, amely automatikusan vonatkozik az Outlook Customer Manager programban létrehozott csoportra. Ha a rendszer egyéni letiltott szót észlel, a rendszer letiltja a csoportlétrehozását az OCM-ben, és a felhasználó nem használja az OCM alkalmazást.
Tanterem alkalmazás | Az Osztályterem alkalmazásban létrehozott csoportok megfelelnek az elnevezési szabályzatnak, de az elnevezési szabályzat nem lesz automatikusan alkalmazva, és az elnevezési szabályzat előnézete nem jelenik meg a felhasználók számára az osztálytermi csoport nevének megadásakor. A felhasználóknak előtagokkal és utótagokkal kell megadniuk az kényszerített osztálytermi csoport nevét. Ha nem, az osztálytermi csoport létrehozása vagy szerkesztése művelet sikertelen lesz a hibákat.
Power BI | A Power BI-munkaterületek megfelelnek az elnevezési szabályzatnak.    
Yammer | Amikor egy felhasználó az Azure Active Directory-fiókjával jelentkezett be a Yammerbe, létrehoz egy csoportot, vagy csoportnevet ír alá, a csoportneve megfelel az elnevezési szabályzatnak. Ez az Office 365-höz kapcsolódó csoportokra és az összes többi Yammer-csoportra egyaránt vonatkozik.<br>Ha az elnevezési házirend létrehozása előtt létrehozott egy Office 365-höz kapcsolódó csoportot, a csoportnév nem követi automatikusan az elnevezési házirendeket. Amikor egy felhasználó szerkéseli a csoport nevét, a rendszer kéri az előtag és az utótag hozzáadását.
StaffHub  | StaffHub csapatok nem követik az elnevezési szabályzatot, de az alapul szolgáló Office 365-csoport nem. StaffHub csapat neve nem alkalmazza az előtagok és utótagok, és nem ellenőrzi az egyéni letiltott szavakat. A StaffHub azonban alkalmazza az előtagokat és utótagokat, és eltávolítja a letiltott szavakat az alapul szolgáló Office 365-csoportból.
Exchange PowerShell | Az Exchange PowerShell-parancsmagok megfelelnek az elnevezési szabályzatnak. A felhasználók megkapják a megfelelő hibaüzeneteket a javasolt előtagokkal és utótagokkal, valamint egyéni letiltott szavakesetén, ha nem követik a csoportnév és a csoportalias (mailNickname) elnevezési házirendjét.
Azure Active Directory PowerShell-parancsmagok | Az Azure Active Directory PowerShell-parancsmagok megfelelnek az elnevezési szabályzatnak. A felhasználók megfelelő hibaüzeneteket kapnak a javasolt előtagokkal és utótagokkal, valamint egyéni letiltott szavakesetén, ha nem követik a csoportnevekben és a csoportaliasokban az elnevezési konvenciót.
Exchange Felügyeleti központ | Az Exchange Felügyeleti központ megfelel az elnevezési szabályzatnak. A felhasználók megfelelő hibaüzeneteket kapnak a javasolt előtagokkal és utótagokkal, valamint egyéni letiltott szavakesetén, ha nem követik a csoportnév ben és a csoportaliasban található elnevezési konvenciót.
Microsoft 365 felügyeleti központ | A Microsoft 365 Felügyeleti központ megfelel az elnevezési szabályzatnak. Amikor egy felhasználó csoportneveket hoz létre vagy ír be, a rendszer automatikusan alkalmazza az elnevezési házirendet, és a felhasználók megfelelő hibákat kapnak, amikor egyéni letiltott szavakat adnak meg. A Microsoft 365 felügyeleti központ még nem jeleníti meg az elnevezési házirend előnézetét, és nem ad vissza egyéni blokkolt szóhibákat, amikor a felhasználó beírja a csoport nevét.

## <a name="next-steps"></a>További lépések

Ezek a cikkek további információkat nyújtanak az Azure AD-csoportokról.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Office 365-csoportok elévülési szabályzata](groups-lifecycle.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
