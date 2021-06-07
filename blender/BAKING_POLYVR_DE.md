# Baking von Texturen für PolyVR

Baking der Lichtinformationen in eine Textur ist sinnvoll, wenn man in einer statischen Szene Lichtinformationen darstellen möchte, die von der Engine nicht runtime dargestellt werden können. Eine genauere Übersicht befindet sich in meinem [Guide](GUIDE.md) zur Erstellung einer PolyVR Szene.

Bitte beachtet, dass ich dieses Tutorial nach meinem Wissensstand und zur aktuellsten Blenderversion Stand 05.2021 erstelle. Auf Fehler dürfen gerne hingewiesen und Ergänzungen dürfen gemacht werden.

Hier befinden sich die Szene dieses Tutorials ohne und mit Berücksichtigung der Lichtinformationen im Vergleich.
Diese Informationen sollen in die Textur eingefangen und in PolyVR verfügbar gemacht werden.

<img src="png/baking_tutorial_mat_setup.png" width="48%">
<img src="png/baking_tutorial_renderprev_02.png" width="48%">

## Szene

Der erste Schritt dieses Tutorials ist eine Szene. Wichtig ist, dass jedes Objekt über eine UV-Map verfügt. Zur besseren Darstellung der Lichtverhältnisse habe ich einen Raum mit einer offenen Wand und Decke gewählt und in den Raum drei Suzannes platziert. 

<img src="png/baking_tutorial_scene_setup.png" width="48%">

## Materialien und Texturen

Den Objekten in der Szene habe ich nahtlose Holztexturen zugewiesen. Zwei der Suzannes leuchten in rot und grün, um zur Veranschauung mehr Lichtinformationen in der Szene zu haben. Ob die Texturen generiert, getiled oder gemapped sind ist für das Baken der Lichtinformationen irrelevant, das finale Ergebnis wird jedoch gemapped sein. Entsprechend sollten für gute Ergebnisse größere Texturen verwendet werden.
Wichtig ist zudem, dass jedes Objekt, auf dem sich am Ende Lichtinformationen befinden soll, (die Suzannes mit Emission sind also ausgenommen) eine UV-Map besitzt. 

<img src="png/baking_tutorial_mat_setup.png" width="48%">

## Vorbereitung

Um das Baking zu starten, muss man eine Bilddatei auswählen. In dieser wird die finale Textur gespeichert.
Möchte man als erstes den Raum baken, wählt man also das Objekt aus, geht in den Materialeditor und erstellt eine einfache [Image Texture Node](https://docs.blender.org/manual/en/2.79/render/cycles/nodes/types/textures/image.html), ohne diese mit einem Shader zu verknüpfen. In der Node erstellt man eine neue Datei und passt sie entsprechend der Anforderungen an (Bildgröße, Alphakanal).

Zusätzlich muss die Renderengine der Szene für das Baking auf Cycles gestellt werden. Ist dies der Fall, befindet sich im "Render Properties" Panel eine Kategorie namens Baking. Hier kann man zwischen verschiedenen Modi wählen. Für uns geeignet ist der Modus "Combined" oder alternativ "Diffuse".

## Baking der Combined Texture

Für das Baking der Informationen auf ein Objekt muss eine Bilddatei ausgewählt sein. Dafür erstellt man, wie im vorherigen Schritt erwähnt, eine Image Texture Node im Material Editor und wählt diese aus. Das Programm wird die Informationen für je ein Material in das dem Material zugehörige ausgewählte Bild speichern. Besitzt ein Stuhl beispielsweise das Material "Holz" an den Beinen und "Stoff" am Polster, muss man in beiden Materialien besagte Image Texture Node erstellen und eine Bilddatei auswählen. Diese kann für beide Materialien die selbe sein, solange sich die UV-Maps der Beine und des Polsters nicht überlappen.

Ist die Bilddatei für alle zu berechnenden Materialien (in unserem Beispiel nur das Holz der Wände) ausgewählt, kann in den Render Properties das Baking gestartet werden. In dem Baking-Modus "Combined" können [Anpassungen](https://docs.blender.org/manual/en/2.79/render/cycles/baking.html?highlight=bake%20combined#bake-mode) für die Szene gemacht werden. Möchte man beispielsweise nicht, dass Informationen zu transluzenten oder reflektierenden Umgebungsobjekten mit einberechnet werden, lassen sich diese abwählen.

Drückt man nun auf Bake, dauert der Prozess je nach Komplexität der Szene mehrere Minuten bis Stunden. Zeit für einen Kaffee :)

<img src="png/baking_tutorial_unbaked_suzanne.png" width="48%">
<img src="png/baking_tutorial_baked_suzanne.png" width="48%">
<img src="png/baking_tutorial_unbaked_walls.png" width="48%">
<img src="png/baking_tutorial_baked_walls.png" width="48%">

## Materialsetup

Sind die fertigen Texturen erstellt, ist wichtig, diese abzuspeichern. Texturen in Blender verschwinden mit Neustart des Programms, sofern sie nicht durch manuelles Abspeichern im Image Editor persistiert worden sind ([Bilder speichern in Blender](https://www.sketchoverflow.com/2020/12/save-image-blender-2-9/)). Möchte man diese Texturen in Blender testen, lässt sich einfach ein zweiter Pfad im Material erstellen, der die neue Textur über einen [Emissionshader](https://docs.blender.org/manual/en/2.79/render/cycles/nodes/types/shaders/emission.html?highlight=emission%20shader) darstellt. Emissionshader sind die geeignete Wahl für eine Combined Textur.

Durch klicken auf verschiedene [Output Nodes](https://docs.blender.org/manual/en/2.79/render/cycles/nodes/types/output/material.html?highlight=material%20output%20node) wechselt man zwischen den unterschiedlichen Shader Pfaden.

<img src="png/baking_tutorial_mats_final.png" width="48%">

## Export

Der übliche Export der Modelle als Collada Dateien funktioniert nicht für Emission Shader. Es ist also notwendig, ein weiteres Material anzulegen, in dem die neue Image Texture an einen Principled BSDF Farbslot angeschlossen ist, bevor man das Objekt exportiert.

