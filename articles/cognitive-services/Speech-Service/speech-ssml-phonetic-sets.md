---
title: Beszéd fonetikus készletek - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan lehet a Beszédszolgáltatás fonetikus ábécé leképezéseit a Nemzetközi Fonetikus Ábécére (IPA), és mikor kell használni, melyik készletet.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675328"
---
# <a name="speech-service-phonetic-sets"></a>Beszédszolgáltatás fonetikus készletei

A Beszédszolgáltatás fonetikus ábécét (röviden"telefonkészleteket") határoz meg, amelyek hét nyelvből állnak; `en-US`, `fr-FR` `de-DE`, `es-ES` `ja-JP`, `zh-CN`, `zh-TW`, , és . A Beszédszolgáltatás telefonja általában a <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Nemzetközi Fonetikus <span class="docon docon-navigate-external x-hidden-focus"> </span>Ábécére (IPA) </a>felel meg. A beszédfelismerési szolgáltatás telefonkészletei a szövegfelolvasó szolgáltatás részeként a [beszédszintetizáló nyelvi (SSML)](speech-synthesis-markup.md)nyelvvel együtt használatosak. Ebből a cikkből megtudhatja, hogy ezek a telefonkészletek hogyan vannak leképezve, és mikor kell használni, hogy melyik telefonkészletet kell használni.

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>Angol szupraszegmentáliák

| 1. példa (Mássalhangzó kirobbanása, szókezdő betű a magánhangzóhoz) | 2. példa (Intervocalic a mássalhangzó, szó mediális nucleus a magánhangzó) | 3. példa (Coda mássalhangzó, szó végleges magánhangzó) | Megjegyzések |
|--|--|--|--|
| burger /b er **1** r - g ax r/ | falafel /f ax - l aa **1** - f ax l/ | gitár /g ih - t aa **1** r/ | Beszéd szolgáltatás telefon meg fel stressz után a magánhangzó a hangsúlyozta szótagja |
| inopportune /ih **2** - n aa - p ax r - t uw 1 n/ | különbözőség /d ih - s ih **2**- m ax - l eh 1 - r ax - t iy/ | munkaerő /w er 1 r k - f ao **2** r s/ | Beszéd szolgáltatás telefon készlet fel stressz után a magánhangzó a sub-hangsúlyozta szótagja |

### <a name="english-vowels"></a>Angol magánhangzók

| `sapi` | `ipa` | 1. példa     | 2. példa | 3. példa                   |
|--------|-------|---------------|-----------|-----------------------------|
| Iy     | `i`   | **ea**t       | **f ee**l  | Vall**ey (vall)**                  |
| Ih     | `ɪ`   | **én**f        | f**i**ll  |                             |
| Ey     | `eɪ`  | **egy**te       | g**a**te  | d**ay**                     |
| Eh     | `ɛ`   | **e**nagyon     | p**e**e e   | m**eh** (ritka szó végül) |
| Ae     | `æ`   | **egy**ctive    | c**a**t   | n**ah** (ritka szó végül) |
| aa     | `ɑ`   | **o**bstinate | p**o**ppy | r**ah** (ritka szó végül) |
| Ao     | `ɔ`   | **o**tartomány    | c**au**se | Ut**ah**                    |
| Uh     | `ʊ`   | b**oo**k      |           |                             |
| Ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| Uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| Ah     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| Ay     | `aɪ`  | **én**ce       | b**i**te  | fl**y (f)**                     |
| Aw     | `aʊ`  | **ou**t       | s**ou**th | c**ow**                     |
| Oy     | `ɔɪ`  | **oi**l       | j**oi**n  | **t oy**                     |
| y uw   | `ju`  | **Ma.**      | h**u**ember | f**ew**                     |
| Ax     | `ə`   | **egy**megy       | wom**a**n | **egy**                    |

### <a name="english-r-colored-vowels"></a>Angol R-színű magánhangzók

| `sapi` | `ipa` | 1. példa    | 2. példa      | 3. példa  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **fül**s     | t**ir**amisu   | n**fül**   |
| eh r   | `ɛɹ`  | **légrepülő** | alkalmazás**ently** | sc**ar**e  |
| uh r   | `ʊɹ`  |              |                | c**az**e   |
| ay r   | `aɪɹ` | **Ire**föld  | f**ir**ehely  | ch**oir**  |
| aw r   | `aʊɹ` | **óra**s    | p**ower**ful   | s**a mi**   |
| ao r   | `ɔɹ`  | **vagy**ange   | m**vagy**al      | s**evező**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **fül**th    | b**ir**d       | f**a**    |
| ax r   | `ɚ`   |              | minden**er**gy    | supp**er** |

### <a name="english-semivowels"></a>Angol félmagánhangzók

| `sapi` | `ipa` | 1. példa           | 2. példa  | 3. példa |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith, s**ue**de | al**w**ays |           |
| é      | `j`   | **y**ard,**f e**w   | az**i-ra**  |           |

### <a name="english-aspirated-oral-stops"></a>Angol szívó orális megáll

| `sapi` | `ipa` | 1. példa | 2. példa   | 3. példa  |
|--------|-------|-----------|-------------|------------|
| P      | `p`   | **p**ut   | ha**pp**hu  | fla**p**   |
| b      | `b`   | **b**ig   | hány**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| n      | `d`   | **d**ig   | futott**d**om  | ro**d**    |
| k      | `k`   | **c**ut   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | egy**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>Angol orrmegállás

| `sapi` | `ipa` | 1. példa        | 2. példa  | 3. példa   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**at, összetör   | ca**m**korszak | roo**m**    |
| p      | `n`   | **n**o, s**n**ow | te**n**t   | csibék**n** |
| Ng     | `ŋ`   |                  | li**n**k   | s**ing**    |

### <a name="english-fricatives"></a>Angol fricatives

| `sapi` | `ipa` | 1. példa   | 2. példa        | 3. példa  |
|--------|-------|-------------|------------------|------------|
| nő      | `f`   | **f**ork    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| Th     | `θ`   | **ez a**    | empa**th**y      | mon**th**  |
| Dh     | `ð`   | **hu**    | mo**th**er       | smoo**th** |
| s      | `s`   | **s**ez     | ri**s**k         | tény**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | gyerek**s**   |
| Sh     | `ʃ`   | **sh e**    | rövidítse**ki** | ru**sh**   |
| Zh     | `ʒ`   | **J**acques | jogalap**s**ure     | gara**g**e |
| ó      | `h`   | **h**elp    | en**h**ance      | **a- h**a!  |

### <a name="english-affricates"></a>Angol hovatartozás

| `sapi` | `ipa` | 1. példa | 2. példa    | 3. példa  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**a  | fu**t**ure   | atta**ch** |
| Jh     | `dʒ`  | **j**oy   | ori**g**inal | oran**g**e |

### <a name="english-approximants"></a>Angol hozzávenek

| `sapi` | `ipa` | 1. példa          | 2. példa  | 3. példa |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id, g**l**ad  | pa**l**ász | chi**ll** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Francia szupraszegmentáliák

A beszéd szolgáltatás telefon meg helyezi a stressz után a magánhangzó a hangsúlyozta szótagja, azonban; a `fr-FR` beszédszolgáltatás telefonkészlete nem támogatja az IPA alstresszt. Ha az IPA alstresszre van szükség, akkor közvetlenül az IPA-t kell használnia.

### <a name="french-vowels"></a>Francia magánhangzók

| `sapi` | `ipa` | 1. példa     | 2. példa       | 3. példa |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **egy**rbire     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p**â te**        | p**a**s   |
| aa ~   | `ɑ̃`  | **en**fant    | enf**en**t      | t**em**ps |
| Ax     | `ə`   |               | **p e**tite      | l**e**    |
| Eh     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| Eu     | `ø`   | **œu**fs      | cr**eu**ser     | qu**eu**  |
| Ey     | `e`   | ému között           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im**portant | p**ein**ture    | mat**in** |
| Iy     | `i`   | **én**dée      | kisállat**i**te      | én**vagyok**   |
| Oe     | `œ`   | **œu**f       | p**eu**r        |           |
| Ó     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| Ó ~   | `ɔ̃`  | **a**ze      | r**a**deur     | b**a**   |
| Ow     | `o`   | **au**diteur  | b**eau**puccs    | p**ô**    |
| oe ~   | `œ̃ ` | **Ensz**        | l**un**di       | br**un**  |
| Uw     | `u`   | ou trage **(trage)**   | intr**ou**vable | **Ou**    |
| Uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>Francia mássalhangzók

| `sapi` | `ipa` | 1. példa   | 2. példa     | 3. példa                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**ille   | ro**b**e                         |
| n      | `d`   | **d**ire    | ron**d**eur   | chau**d**e                       |
| nő      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g auche**  | é**g**ale     | ba**gu**e                        |
| Ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)park**ing** |
| Hy     | `ɥ`   | h**u**ile   | n**u**ire     |                                  |
| k      | `k`   | **c**arte   | é**c**aille   | kell**c**                          |
| l      | `l`   | **l**ong    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**m**er     | po**mm**e                        |
| p      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| Nj     | `ɲ`   |             |               | pei**gn**e                       |
| P      | `p`   | **p**atte   | újra**p**     | ca**p**                          |
| r      | `ʁ`   | **r, itt:**     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | a**ss**ez     | pa**ss**e                        |
| Sh     | `ʃ`   | **ch**harisnyakötő | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | **t**ête    | Ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | **v-ben**írja be  | rê**v**e                         |
| w      | `w`   | **ou**i     | **f ou**ine    |                                  |
| é      | `j`   | **y**od     | p**i**étiner  | Marse**betegség**                    |
| z      | `z`   | **z **éro   | rai**s**onner | ro**s**e                         |
| Zh     | `ʒ`   | **j**ardin  | férfi**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d**                        |
|        | `z‿`  |             |               | di**x betű**                          |

<a id="fr-1"></a>
**1** *Csak néhány idegen szó.*

> [!TIP]
> A `fr-FR` Beszédszolgáltatás telefonkészlete nem támogatja a következő `n‿`francia `t‿`kapcsolattartási műveleteket, a és `z‿`a . Ha szükség van rájuk, fontolja meg az IPA közvetlen használatát.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Német szupraszegmentáliák

| 1. példa (Mássalhangzó kirobbanása, szókezdő betű a magánhangzóhoz) | 2. példa (Intervocalic a mássalhangzó, szó mediális nucleus a magánhangzó) | 3. példa (Coda mássalhangzó, szó végleges magánhangzó) | Megjegyzések |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologie /b iy - ow - l ow - g iy **1**/ | Beszéd szolgáltatás telefon meg fel stressz után a magánhangzó a hangsúlyozta szótagja |
| Allgemeinwissen /a **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | Beszéd szolgáltatás telefon készlet fel stressz után a magánhangzó a sub-hangsúlyozta szótagja |

### <a name="german-vowels"></a>Német magánhangzók

| `sapi` | `ipa`     | 1. példa                             | 2. példa     | 3. példa                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| A:     | `aː`      | **A**ber                              | Maßst**a**b   | A**séma**                         |
| a      | `a`       | **A**bfall                            | B**a**ch      | Agath**a**                         |
| Ó     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| Eh:    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1 1</sup>](#de-v-1) Fasci**ae között** |
| Eh     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| Ax     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | Frag**e**                          |
| Iy     | `iː`      | **Futottam**                              | abb**ie**gt   | Relativitätstheor**azaz**            |
| Ih     | `ɪ`       | **Én**nnung                            | s**i**ngen    | Fa**y**                          |
| Eu     | `øː`      | **Ö**sen                              | abl**ö**sten  | Malm**ö**                          |
| Ow     | `o`, `oː` | **o**hne                              | Balk**o**n    | Trept**ow**                        |
| Oe     | `œ`       | **Ö**ffnung                           | bef**ö**rdern |                                    |
| Ey     | `e`, `eː` | **E**berhard között                          | abf**e**gt    | b                                  |
| Uw     | `uː`      | **U**csinál                               | H**u**t       | Akk**u**                           |
| Uh     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| Ue     | `yː`      | **Ü**bermut                           | pfl**ü**gt    | Férfiak**ü**                           |
| Uy     | `ʏ`       | **ü**ppig                             | S**y**szár    |                                    |

<a id="de-v-1"></a>
**1** *Csak idegen eredetű szavakkal, mint például: Fasci**ae**.*<br>
<a id="de-v-2"></a>
**2** *Word-intially csak szavakkal idegen eredetű, mint a **ppointment.** Szógalyaz elsőben: 'v**e**rstauen.*

### <a name="german-diphthong"></a>Német diphthong

| `sapi` | `ipa`       | 1. példa    | 2. példa          | 3. példa |
|--------|-------------|--------------|--------------------|-----------|
| Ay     | `ai`        | **ei**nsam között   | Unabhängigk**ei**t | Abt**ei** |
| Aw     | `au`        | **au**ßen    | abb**au**st        | St**au**  |
| Oy     | `ɔy`, `ɔʏ̯` | Eu phorie **(Eu**phorie) | tr**äu**mt         | sch**eu** |

### <a name="german-semivowels"></a>Német félmagánhangzók

| `sapi` | `ipa` | 1. példa | 2. példa    | 3. példa  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | zárolás**er** |

### <a name="german-consonants"></a>Német mássalhangzók

| `sapi` | `ipa` | 1. példa | 2. példa | 3. példa |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1 1</sup>](#de-c-1) Pu**b** |  |
| c | `ç` | **Ch**emie (Ch emie) | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| n | `d` | **d**anken | [<sup>3. 20 0</sup>](#de-c-3) Len**d**l | [<sup>4.</sup>](#de-c-4) Clau**d**e |  |
| Jh | `ʤ` | **J**eff | gemana**g**t | [<sup>5.</sup>](#de-c-5) **Csaba e** |
| nő | `f` | **F**ahrtdauer között | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**ut |  | [<sup>6.</sup>](#de-c-6) Gre**g (g)** |  |
| ó | `h` | **H**ausanbau |  |  |  |
| é | `j` | **J**od között | Reakt**én** | hu**én** |  |
| k | `k` | **K**oma | Aspe**k**t | Flec**k** |  |
| l | `l` | **l**au | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**ut | A**m**t | Leh**m** |  |
| p | `n` | **n**un | u**n**d | Huh**n** |  |
| Ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng**uyen | Schwa**nk között** | R**ing között** |  |
| P | `p` | **P**artner | abru**p**t | Ti**p** |  |
| Pf | `pf` | **Pf**érde | gát**pf**t | A**pf** |  |
| r | `ʀ`, `r`, `ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| Sh | `ʃ` | **Sch**ule között | mi**sch t** | lappi**sch** |  |
| t | `t` | **T**raum között | S**t**raße | Mu**t** |  |
| Ts | `ts` | **Z**ug | Ar**z**t | Szellemes**z** |  |
| ch | `tʃ` | **Tsch**echien között | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | [<sup>9.</sup>](#de-c-9) Gr**oo**ve |  |
| x | [<sup>10,</sup>](#de-c-10)`x`[<sup>11</sup>](#de-c-11)`ç` | [<sup>12.</sup>](#de-c-12) Ba**ch**erach | Ma**ch**t mögli**ch**st | Schma**ch** 'i**ch** |
| z | `z` | **s**uper |  |  |  |
| Zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g**e |

<a id="de-c-1"></a>
**1** *Csak idegen eredetű szavakkal, mint például: Pu**b**.*<br>
<a id="de-c-2"></a>
**2** *Lágy "ch" után "e" és "i"*<br>
<a id="de-c-3"></a>
**3** *Csak idegen eredetű szavakkal, mint például: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *Csak idegen eredetű szavakkal, mint például: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *Csak idegen eredetű szavakkal, mint például: Chan**g**e.*<br>
<a id="de-c-6"></a>
**6** *Word-terminálisan csak szavakkal idegen eredetű, mint a Gre**g**.*<br>
<a id="de-c-7"></a>
**7** *Csak idegen eredetű szavakkal, mint például: **Ng**uyen.*<br>
<a id="de-c-8"></a>
**8** *Csak idegen eredetű szavakkal, mint például: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *Csak idegen eredetű szavakkal, mint például: Gr**oo**ve.*<br>
<a id="de-c-10"></a>
**10** *Az `x` IPA egy kemény "ch" után minden nem-front magánhangzók (a, aa, ó, ow, uh, uw és a diphthong aw).*<br>
<a id="de-c-11"></a>
**11** *Az `ç` IPA az első magánhangzók (ih, iy, eh, ae, uy, ue, oe, eu is diphthongs ay, oy) és mássalhangzók után*<br>
<a id="de-c-12"></a>
**12** *Word-kezdetben csak a szavak idegen eredetű, mint például: **J**uan. Szógaló-kezdetben is olyan szavakkal, mint: Ba**ch**erach.*<br>

### <a name="german-oral-consonants"></a>Német szóbeli mássalhangzók

| `sapi` | `ipa` | 1. példa                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich /b fejsze - ^ a 1 x t - l ih c/ |

> [!NOTE]
> Hozzá kell adnunk\] egy [gs telefont két különböző magánhangzó közé, kivéve, hogy a két magánhangzó valódi diphthong. Ez a szóbeli mássalhangzó egy glottal stop, <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">további információkért <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>lásd glottal stop .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Spanyol magánhangzók

| `sapi` | `ipa` | 1. példa    | 2. példa     | 3. példa    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **egy**lto     | c**a**ntar    | cas**a**     |
| i      | `i`   | **i**bérica  | av**i**gyógyfürdő    | adó**i**     |
| e      | `e`   | **e**lefante | -nál**e**nto    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| U      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>Spanyol mássalhangzók

| `sapi` | `ipa`      | 1. példa  | 2. példa      | 3. példa      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| ch     | `tʃ`       | **ch**eque | co**ch**e      | Marraque**ch között** |
| n      | `d`        | **d**edo   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d**     |
| nő      | `f`        | **f**ácil  | ele**f**ante   | pu**f**        |
| g      | `g`        | **g**anga  |                | dópin**g**     |
|        | `ɣ`        |            | egy**g**ua       | tuare**g**     |
| J      | `j`        | **én**odo   | cal**én**ente   | re**y**        |
| Jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**oche  | bo**c**a       | titáni**c**    |
| l      | `l`        | **l**ápiz  | a**l**a        | corde**l**     |
| Ll     | `ʎ`        | **ll**ave  | desarro**ll**o |                |
| m      | `m`        | **m**rendelés | a**m**ar       | álbu**m**      |
| p      | `n`        | **n**ada   | **ce n**a       | rató**n**      |
| Nj     | `ɲ`        | **ñ**aña   | ara**ñ**azo    |                |
| P      | `p`        | **p**oca   | **p**o-ra       | sto**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| Rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| s      | `s`        | **s aco**   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | a**t**ar       | disque**t**    |
| Th     | `θ`        | **z**ebra  | a**z**ul       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u**       |
| x      | `x`        | **j**ota   | a**j**o        | relo**j**      |

> [!TIP]
> A `es-ES` Beszédszolgáltatás telefonkészlete nem támogatja a `β`következő `ð`spanyol `ɣ`IPA, , és . Ha szükség van rájuk, fontolja meg az IPA közvetlen használatát.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

A Beszédszolgáltatás `zh-CN` telefonkészlete a <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">Pinyin <span class="docon docon-navigate-external x-hidden-focus"></span> </a> készülék natív telefonján alapul.

### <a name="tone"></a>Hangnem

| Pinyin-hang | `sapi` | Példa karakterre |
|-------------|--------|-------------------|
| mā          | ma 1  | - Nem, nem,                 |
| má          | ma 2  | - Nem, nem,                 |
| mё között          | ma 3  | A (198                 |
| ma          | ma 4  | - Nem, nem,                 |
| ma          | ma 5  | - Nem, nem,                 |

#### <a name="example"></a>Példa

| Karakter | Speech szolgáltatás                |
|-----------|-------------------------------|
| A (1)      | zu 3 - zhi 1 - guan 1 - xi 5 |
| - Nem, nem, nem, nem        | lej 3 -jin 4                 |
| A       | xi 1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

A Beszéd szolgáltatás `zh-TW` telefon készlet van készlet -ra van azon alapszik a bennszülött telefon <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> készlet.

### <a name="tone"></a>Hangnem

| Beszédszolgáltatás hangneme | Bopomofo-hang | Példa (szó) | Beszédszolgáltató telefonok | Bopomofo között | Pinyin (ン) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| - Nem, nem,                   | üres         | - Nem, nem,              | A (sz)                   | - Nem, nem, nem, nem       | zhēn között        |
| - Nem, nem,                   | - Nem, nem,             | - Nem, nem,              | A (sz)                   | A (sz)      | chá között         |
| A (z)                   | A (z)             | 啦              | 啦-                   | 啦-      | dasztika          |
| - Nem, nem,                   | - Nem, nem,             | - Nem, nem,              | A (sz)                   | A (sz)      | wàng között        |
| ˙                   | ˙             | 啦ンン             | 啦ンンンンン               | 啦ンンンンン  | yёng zi    |

#### <a name="example"></a>Példa

| Karakter | `sapi`   |
|-----------|----------|
| - Nem, nem,         | ㄍㄡˇ      |
| A ( d)        | A (sz)   |
| - Nem, nem, nem, nem        | A (sz) |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

A Beszédszolgáltatás `ja-JP` telefonkészlete a <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana <span class="docon docon-navigate-external x-hidden-focus"></span> </a> készülék natív telefonján alapul.

### <a name="stress"></a>Stressz

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`főstressz |
| `+`    | `ˌ`alstressz  |

#### <a name="example"></a>Példa

| Karakter | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 啦ン        | A(z)    | goajszeji   |
| 啦ンン       | A következő? | 啦 joj啦ンンンja |
| A (1)       | A sz.  | sajitecika |

***