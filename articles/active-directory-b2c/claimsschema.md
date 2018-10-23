---
title: ClaimsSchema – az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az egyéni szabályzat ClaimsSchema elem Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 92328ffd8b6dbbb2be82bc70352e19f3097eb2a7
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637731"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **ClaimsSchema** elem definiálja, amely lehet hivatkozni a jogcímtípusok, amelyeket a szabályzat részeként. Jogcímek séma az a hely, ahol, kijelenti, hogy a jogcímek. Jogcím Utónév, utolsó nevét, megjelenített név, telefonszám és több is lehet. ClaimsSchema elem listáját tartalmazza **takar** elemeket. A **takar** elem tartalmazza a **azonosító** attribútum, amely a jogcím neve. 

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>Takar

A **takar** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A jogcímtípus használt azonosító. Más elemek használhatja ezt az azonosítót a házirendben. |

A **takar** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| displayName | 0:1 | Különböző képernyőkön felhasználók számára megjelenő cím. Az érték lehet [honosított](localization.md). |
| Adattípus | 0:1 | A jogcím típusa. A logikai érték, date, dateTime, int, hosszú adattípusok, string, Publishedresources, alternativeSecurityIdCollection is használható. |
| DefaultPartnerClaimTypes | 0:1 | A partner alapértelmezett jogcímtípusok megadott protokoll használatára. Az érték felülírható legyen a a **PartnerClaimType** megadott a **bemeneti jogcím** vagy **kimeneti jogcím** elemeket. Ez az elem használatával adja meg egy protokoll alapértelmezett nevét.  |
| Maszk | 0:1 | Egy nem kötelező karakterlánc maszkolás karaktereket is alkalmazható a jogcím megjelenítésekor. Ha például a telefon száma 324-232-4343 is maszkolva XXX-XXX-4343. |
| UserHelpText | 0:1 | A jogcím típusa, amely a felhasználóknak megérteni célra leírása. Az érték lehet [honosított](localization.md). |
| UserInputType | 0:1 | A felhasználó számára elérhetőnek kell lennie, amikor a jogcím-adatok manuális megadása a jogcím típusa szövegbeviteli vezérlőelem típusa. Tekintse meg később ezen a lapon definiált felhasználói bemeneti típusnak. |
| Korlátozás | 0:1 | Az érték korlátozások a jogcím, például egy reguláris kifejezés (Regex) vagy az elfogadható értékek listáját. Az érték lehet [honosított](localization.md). |
PredicateValidationReference| 0:1 | Egy hivatkozást egy **PredicateValidationsInput** elemet. A **PredicateValidationReference** elemek lehetővé teszik annak érdekében, hogy csak a megfelelően formázott adatokat is meg kell adni egy érvényesítési folyamat végrehajtásához. További információkért lásd: [predikátumokat](predicates.md). |

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

A **DefaultPartnerClaimTypes** tartalmazhatja a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Protokoll | 0: n | Alapértelmezett partnereikkel együtt protokollokat jogcím-típus neve. |

A **protokoll** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Name (Név) | Igen | Azure AD B2C által támogatott érvényes protokoll neve. Lehetséges értékek a következők: OAuth1, az OAuth2, egy SAML2, OpenIdConnect, WsFed vagy WsTrust. |
| PartnerClaimType | Igen | A jogcím típusa használni kívánt nevét. |

A következő példában az identitás-kezelőfelületi keretrendszer együttműködő identitásszolgáltató egy SAML2 vagy függő gyártótól származó alkalmazás, amikor a **Vezetéknév** jogcím van leképezve `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`, OpenIdConnect és OAuth2, az a jogcím nem leképezve `family_name`.

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Ennek eredményeképpen az Azure AD B2C által kiállított JWT jogkivonat bocsát ki a `family_name` takar neve helyett **Vezetéknév**.
 
```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Maszk

A **maszk** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Típus | Igen | Az igényt maszk típusa. A lehetséges értékek: `Simple` vagy `Regex`. A `Simple` érték azt jelzi, hogy egy egyszerű szöveges maszkot a vezető része egy karakterlánc jogcím van hozzárendelve. A `Regex` érték azt jelzi, hogy a teljes karakterlánc jogcím van hozzárendelve egy reguláris kifejezést.  Ha a `Regex` érték van megadva, nem kötelező attribútum kell definiálni a reguláris kifejezés használata. |
| reguláris kifejezés | Nem | Ha **típus** értékre van állítva `Regex`, adja meg a reguláris kifejezés használata.

A feloldását kérte példa konfigurálja egy **PhoneNumber** jogcím a `Simple` maszk:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>  
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Az identitás-kezelőfelületi keretrendszer rendereli a telefonszám során az első hat számjegy elrejtése:

![Használatával jogcím maszkkal rendelkező típusa](./media/claimsschema/mask.png)

A feloldását kérte példa konfigurálja egy **AlternateEmail** jogcím a `Regex` maszk:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Az identitás-kezelőfelületi keretrendszer Ez a beállítás csak az első betűje az e-mail-cím és az e-mailek tartománynév:

![Használatával jogcím maszkkal rendelkező típusa](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Korlátozás

A **korlátozás** elemet tartalmazhatnak a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| MergeBehavior | Nem | Egy takar egy szülő szabályzatban ugyanezzel az azonosítóval rendelkező értékeket egyesítési módját. Akkor használja ezt az attribútumot, ha felülírja az alap szabályzatban megadott jogcím. A lehetséges értékek: `Append`, `Prepend`, vagy `ReplaceAll`. A `Append` értéke a szülő házirendben megadott gyűjtemény végére hozzáfűzni adatok gyűjteménye. A `Prepend` értéke a szülő házirendben megadott gyűjtemény előtt az új adatok gyűjteménye. A `ReplaceAll` értéke figyelmen kívül lesz hagyva a szülő házirendben megadott adatok gyűjteménye. |

A **korlátozás** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Enumerálás | 1: n | A felhasználó egy jogcímet, például egy értéket a legördülő listában válassza ki az elérhető beállítások a felhasználói felületen. |
| Mintázat | 1:1 | A reguláris kifejezés használata. |

### <a name="enumeration"></a>Enumerálás

A **enumerálás** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Szöveg | Igen | A megjelenítendő karakterlánc, amely a felhasználói felületen ezt a lehetőséget választja a felhasználó számára látható. |
|Érték | Igen | A jogcím értéke, amely ezzel a beállítással társítva van. |
| SelectByDefault | Nem | Azt jelzi-e ez a beállítás van kiválasztva a felhasználói felület alapértelmezés szerint. A lehetséges értékek: True vagy FALSE (hamis). |

Ez a példa konfigurálja egy **Város** legördülő lista jogcímet, és a egy alapértelmezett értéket az `New York`:

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```
Város legördülő alapértelmezett értékkel, New York-i beállítása:

![Város legördülő](./media/claimsschema/dropdownsingleselect.png)


### <a name="pattern"></a>Mintázat

A **minta** elem magában foglalhatja a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Válaszban | Igen | Az ilyen típusú jogcímek egyeznie kell annak érdekében, hogy érvényes reguláris kifejezés. |
| Súgószöveg | Nem | A minta vagy ezt az igényt a reguláris kifejezés. |

Konfigurálja az alábbi példa egy **e-mail** jogcímet reguláris kifejezés bemeneti érvényesítési és súgó szövege:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Az identitás-kezelőfelületi keretrendszer jelenik meg az e-mail cím jogcímet e-mail formátum bemenet-ellenőrzés:

![A minta használata a jogcímtípusú](./media/claimsschema/pattern.png)

## <a name="userinputtype"></a>UserInputType

Az Azure AD B2C felhasználói bemeneti típusai, egy szövegmező, a jelszót és a legördülő lista használható, amikor jogcímadatokat manuális megadása a jogcím típusa például számos támogat. Meg kell adnia a **UserInputType** begyűjtésekor információkat a felhasználó a használatával egy [önálló kiszolgáló által megerősített technikai profil](self-asserted-technical-profile.md).

### <a name="textbox"></a>TextBox

A **szövegmező** felhasználói adatbevitel típusa szolgál egy egysoros szövegmezőben adja meg.

![Használatával jogcím a beviteli mező típusa](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

### <a name="emailbox"></a>EmailBox

A **EmailBox** felhasználói adatbevitel típusa egy alapszintű e-mail beviteli mező biztosítására szolgál.

![A emailbox jogcímtípusú használatával](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

### <a name="password"></a>Jelszó

A **jelszó** felhasználói adatbevitel típusa segítségével rögzíti a felhasználó által megadott jelszót.

![A jelszó használata a jogcímtípusú](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

### <a name="datetimedropdown"></a>DateTimeDropdown

A **DateTimeDropdown** felhasználói adatbevitel típusa legördülő listákból válassza ki a nap, hónap és év körét szolgál. Predikátumok és PredicateValidations elemek segítségével szabályozhatja a dátum minimális és maximális értékeket. További információkért lásd: a **konfigurálása egy adott időtartományban** szakaszában [predikátumok és PredicateValidations](predicates.md).

![A datetimedropdown jogcímtípusú használatával](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

### <a name="radiosingleselect"></a>RadioSingleSelect

A **RadioSingleSelect** felhasználói adatbevitel típusa adjon meg egy gyűjteményt, amely lehetővé teszi a felhasználó számára az egyik lehetőség választógombok szolgál.

![A radiodsingleselect jogcímtípusú használatával](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>    
```

### <a name="dropdownsingleselect"></a>DropdownSingleSelect

A **DropdownSingleSelect** felhasználói adatbevitel típusa segítségével adja meg a legördülő listából, amely lehetővé teszi a felhasználó számára az egyik lehetőség.

![A dropdownsingleselect jogcímtípusú használatával](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

A **CheckboxMultiSelect** felhasználói adatbevitel típusa, amely lehetővé teszi a felhasználó számára több lehetőséggel jelölőnégyzetek olyan szolgál.

![A checkboxmultiselect jogcímtípusú használatával](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="readonly"></a>csak olvasható

A **Readonly** felhasználói adatbevitel típusa adja meg a csak olvasható a mező a jogcímek és az érték szolgál.

![A csak olvasható jogcímtípusú használatával](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


### <a name="paragraph"></a>Bekezdés

A **bekezdés** felhasználói adatbevitel típusa segítségével adjon meg egy olyan mező, a szöveg csak egy bekezdéscímkén jeleníti meg. Ha például &lt;p&gt;szöveg&lt;/p&gt;.

![A bekezdés jogcímtípusú használatával](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

Jeleníti meg a **enumerálás** érték egy **responseMsg** jogcím, használja a `GetMappedValueFromLocalizedCollection` vagy `CreateStringClaim` jogcím-átalakítás. További információkért lásd: [karakterlánc jogcímek átalakítása](string-transformations.md) 
