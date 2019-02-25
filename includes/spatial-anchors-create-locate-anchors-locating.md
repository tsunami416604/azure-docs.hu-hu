## <a name="locating-a-cloud-spatial-anchor"></a>A térbeli felhőbeli horgony megkeresése

Térbeli felhőbeli horgonyok megkereséséhez kell ismernie a azonosítókról. Jegyzetobjektum azonosítók az a alkalmazás s háttérszolgáltatásban tárolt, és elérhető-e az összes eszközre, hogy megfelelően hitelesíteni tudják magukat, lehet. Lásd a példát [oktatóanyag: Térbeli horgonyok megosztása eszközök](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Egy AnchorLocateCriteria objektumpéldányt, és állítsa az azonosítók keres, és a CreateWatcher metódus meghívása a munkamenetben a AnchorLocateCriteria megadásával.
