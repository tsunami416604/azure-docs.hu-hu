---
title: UserJourneys |} A Microsoft Docs
description: Adja meg az egyéni szabályzat UserJourneys elem Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 20ca4b9d347b9dc01e3b890fcf3758fb2fb135b9
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486138"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Felhasználói utak explicit elérési utak, amelyen keresztül egy szabályzatot a szerezze be a kívánt jogcím egy felhasználó egy függő fél alkalmazást lehetővé teszi, hogy adja meg. A felhasználó lekéréséhez, amelyeket a függő entitás számára megjelenő veszi az elérési utak keresztül. Más szóval felhasználói utak meghatározása egy végfelhasználó halad át az Azure AD B2C-vel identitás-kezelőfelületi keretrendszer folyamatok, a kérelem üzleti logikáját.

Ezek az felhasználói utak elérhető a különféle függő entitások felek a Közösség fontos a mag igényeinek kielégítése sablonok tekinthető meg. Felhasználói utak lehetővé teszik a definíció egy szabályzatot a függő entitás részét. Egy házirend több felhasználói utak adhatja meg. Minden egyes felhasználói interakciósorozat vezénylési lépéseit.

A szabályzat által támogatott felhasználói út meghatározása egy **UserJourneys** elem kerül a házirend-fájl a legfelső szintű elem alatt. 

A **UserJourneys** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Felhasználói út, amely meghatározza az összes szükséges teljes körű felhasználói folyamat szerkezeteket. | 

A **UserJourney** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Egy arra mutató hivatkozás a szabályzat más elemek szolgáló felhasználói út azonosítója. A **DefaultUserJourney** eleme a [függő fél házirend](relyingparty.md) ezt az attribútumot mutat. |

A **UserJourney** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Egy vezénylési sorozat, amely egy sikeres tranzakció kell követnie. Minden felhasználói interakciósorozat sorrendben végrehajtott vezénylési lépésekből rendezett listáját tartalmazza. Ha valamelyik lépés meghiúsul, a tranzakció sikertelen lesz. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Felhasználói út jelenik meg egy vezénylési feladatütemezés sikeres tranzakció kell követnie. Ha valamelyik lépés meghiúsul, a tranzakció sikertelen lesz. Vezénylési lépések hivatkozhat mindkét építőelemeket, és a Jogcímszolgáltatók engedélyezett a házirend-fájlban. Bármely vezénylési lépés, amelyek felelős megjelenítése vagy jelennek meg a felhasználói élmény is tartalmaz egy hivatkozást a megfelelő tartalomdefiníció-azonosítóval.

Vezénylési lépésekből feltételesen hajtható végre, a vezénylési lépés elemben definiált előfeltételeket alapján. Például ellenőrizheti, hogy egy vezénylési lépés végrehajtása csak akkor, ha egy adott jogcímek létezik, vagy ha jogcím értéke, vagy nem a megadott érték. 

A vezénylési lépésekből, rendezett listáját adja meg egy **OrchestrationSteps** elem kerül a szabályzat részeként. Ez az elem megadása kötelező.

A **OrchestrationSteps** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | Egy rendezett vezénylési lépés. | 

A **OrchestrationStep** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| `Order` | Igen | A vezénylési lépéseinek sorrendjét. | 
| `Type` | Igen | A vezénylési lépés típusát. Érvényes értékek: <ul><li>**Hiányzik a ClaimsProviderSelection** -azt jelzi, hogy megjelennek-e a vezénylési lépés különböző jogcímszolgáltatóktól a felhasználó kiválaszthat egy számára.</li><li>**CombinedSignInAndSignUp** -azt jelzi, hogy a vezénylési lépés bemutatja egy kombinált közösségi szolgáltató be- és a helyi fiók regisztrálási oldala.</li><li>**ClaimsExchange** -azt jelzi, hogy a vezénylési lépés adatcseréihez használható-e jogcímeket egy jogcímszolgáltatótól.</li><li>**SendClaims** -azt jelzi, hogy a vezénylési lépés a jogcímek kiállítója által kiállított jogkivonatok küld a jogcímeket a függő entitáshoz.</li></ul> | 
| ContentDefinitionReferenceId | Nem | Az azonosítója a [definíciós tartalom](contentdefinitions.md) a vezénylési lépés társított. Általában a tartalomdefiníció Referenciaazonosító definiálva van az önellenőrzött technikai profilban. De vannak bizonyos esetekben az Azure AD B2C-vel valami technikai profil nélkül megjeleníthető van szükség. Nincsenek két példa, ha a vezénylési lépés típusát a következők egyikét: `ClaimsProviderSelection` vagy `CombinedSignInAndSignUp`. Az Azure AD B2C kell megjeleníteni az identity provider kijelölés technikai profil nélkül. | 
| CpimIssuerTechnicalProfileReferenceId | Nem | A vezénylési lépés típusa `SendClaims`. Ez a tulajdonság határozza meg, hogy a jogcímszolgáltató jogcímszolgáltató-kibocsátja a jogkivonatot a függő entitás technikai profil azonosítója.  Ha hiányoznak, nem megbízható függő entitás jogkivonat jön létre. |


A **OrchestrationStep** elem magában foglalhatja a következő elemeket:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- | 
| Az Előfeltételek | 0:n | A vezénylési lépés végrehajtásához kell biztosítani az Előfeltételek listáját. | 
| ClaimsProviderSelections | 0:n | A jogcímeket szolgáltató beállításokat a vezénylési lépés listája. | 
| ClaimsExchanges | 0:n | A vezénylési lépés jogcím cseréje egy listája. | 

### <a name="preconditions"></a>Az Előfeltételek

A **előfeltételeket** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- | 
| Előfeltétel | 0:n | Attól függően, a technikai profil használja vagy átirányítja az ügyfelet, a jogcímeket szolgáltató kiválasztása vagy révén az Exchange server hívás jogcímek megfelelően. | 


#### <a name="precondition"></a>Előfeltétel

A **előfeltétel** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| `Type` | Igen | Ügyfélellenőrzés, vagy a lekérdezés végrehajtásához az előfeltétel típusa. Az érték lehet **ClaimsExist**, amely megadja, hogy a műveleteket kell elvégezni, ha a megadott jogcím szerepel a felhasználó aktuális jogcímek készletében, vagy **ClaimEquals**, ami azt jelenti, hogy a műveletek kell elvégezni, ha a megadott jogcím létezik, és annak értéke a megadott érték egyenlő. |
| `ExecuteActionsIf` | Igen | Egy igaz vagy hamis teszt segítségével döntse el, ha a másnak az előfeltétele a műveleteket kell elvégezni. | 

A **előfeltétel** elemeket a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Érték | 1:n | A lekérdezendő ClaimTypeReferenceId. Egy másik értéket elem tartalmazza az ellenőrizendő értékkel.</li></ul>|
| Műveletek | 1:1 | Az előfeltétel-ellenőrzés belül egy vezénylési lépés teljesülése esetén végrehajtandó művelet. Ha az érték a `Action` értékre van állítva `SkipThisOrchestrationStep`, a társított `OrchestrationStep` nem hajtható végre. | 

#### <a name="preconditions-examples"></a>Az Előfeltételek példák

Az alábbi előfeltételeket ellenőrzi, hogy létezik-e a felhasználó objectId. A felhasználói interakciósorozatban szereplő a helyi fiókkal bejelentkezni a felhasználó által kiválasztott. Ha az objectId létezik, hagyja ki a vezénylési lépés.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Az alábbi előfeltételeket ellenőrzi-e a felhasználó be egy közösségi fiókkal jelentkezett-e. Kísérlet történik meg a felhasználói fiókot a címtárban. Ha a felhasználó bejelentkezik, vagy helyi fiókkal regisztrál kihagyja, az előkészítési lépés.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Az Előfeltételek ellenőrzéséhez több előfeltételeket. Az alábbi példa ellenőrzi, hogy "objectId" vagy "e-mail" létezik-e. Ha az első feltétel teljesül, az út a következő vezénylési lépés kihagyja.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>      
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Egy vezénylési lépés típusú `ClaimsProviderSelection` vagy `CombinedSignInAndSignUp` tartalmazhat, amelyek egy felhasználó bejelentkezhessen a Jogcímszolgáltatók listáját. Az elemek sorrendjét a `ClaimsProviderSelections` elemek szabályozza az identitás-szolgáltatóktól, a felhasználó számára megjelenő sorrendjét.

A **hiányzik a ClaimsProviderSelection** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 0:n | Választható Jogcímszolgáltatók listáját jeleníti meg.|

A **hiányzik a ClaimsProviderSelection** elem tartalmazza a következő attribútumokat: 

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Nem | A következő vezénylési lépés a jogcímeket szolgáltató kijelölés a jogcímcsere azonosítóját. Ez az attribútum vagy ValidationClaimsExchangeId attribútum megadott, de nem mindkettőn kell lennie. | 
| ValidationClaimsExchangeId | Nem | Az aktuális vezénylési lépés a jogcímeket szolgáltató kijelölés érvényesítése a jogcímcsere azonosítóját. Ez az attribútum vagy TargetClaimsExchangeId attribútum megadott, de nem mindkettőn kell lennie. |

### <a name="claimsproviderselection-example"></a>Hiányzik a ClaimsProviderSelection példa

A következő vezénylési lépés a felhasználó választhatja ki, jelentkezzen be, a Facebook, LinkIn, Twitter, Google vagy helyi fiók. Ha a felhasználó egy közösségi identitásszolgáltatóval, a második vezénylési lépés szoftverrel végrehajtja a megadott jogcímértékeket exchange, a `TargetClaimsExchangeId` attribútum. A második vezénylési lépés a felhasználó a bejelentkezési folyamat befejezéséhez a közösségi identitásszolgáltató irányítja át. Ha a felhasználó úgy dönt, hogy jelentkezzen be a helyi fiókkal, az Azure AD B2C-vel (a azonos előfizetéshez vagy a bejelentkezési oldalon) azonos vezénylési lépés marad, és kihagyja a második vezénylési lépés.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange" 
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

A **ClaimsExchanges** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| ClaimsExchange | 0:n | Attól függően, a technikai profil használja vagy átirányítja az ügyfelet a ClaimsProviderSelection kiválasztott vagy révén az Exchange server hívás jogcímek megfelelően. | 

A **ClaimsExchange** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A jogcímek exchange lépés azonosítója. Az azonosító hivatkozást a jogcímeket szolgáltató kijelölés jogcímcsere. lépés: a házirend szolgál. | 
| TechnicalProfileReferenceId | Igen | A futtatandó technikai profil azonosítója. |
