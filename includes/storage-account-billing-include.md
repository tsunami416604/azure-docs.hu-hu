Az Azure Storage használatáért a tárfiók alapján kell fizetni. A tárolási költségeket az alábbi tényezők határozzák meg: a régió/hely, a fiók típusa, a tárolási kapacitás, a replikálási séma, a tárolási tranzakciók és a kimenő adatforgalom.

- A régió alatt az a földrajzi régió értendő, amelyben a fiókja található.
- A fiók típusa arra utal, hogy általános célú tárfiókot vagy Blob Storage-fiókot használ-e. Blob Storage-fiók esetén a hozzáférési szint határozza meg a fiók számlázási modelljét is.
- A tárolási kapacitás azt jelzi, hogy tárfiókja mekkora részét használja adatok tárolására.
- A replikálás határozza meg, hogy adatait egyszerre hány példányban és hol tárolja.
- A tranzakciók az összes olvasási és írási műveletet jelentik az Azure Storage szolgáltatásban.
- A kimenő adatforgalom az adott Azure-régiókból kifelé irányuló adatátvitelt jelenti. Amikor a tárfiókban lévő adatokat egy olyan alkalmazás használja, amely nem ugyanabban a régióban fut, a kimenő adatforgalom díjköteles. (Az Azure szolgáltatások esetében adatait és szolgáltatásait azonos adatközpontba csoportosítva csökkentheti vagy elkerülheti a kimenő adatforgalom díját.)

Az [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lap részletes információkat biztosít a fióktípussal, a tárolási kapacitással, a replikálással és a tranzakciókkal kapcsolatban. Az [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) tartalmazza a kimenő adatforgalommal kapcsolatos részletes díjszabási információkat. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) használatával megbecsülheti költségeit.


<!--HONumber=Sep16_HO4-->


