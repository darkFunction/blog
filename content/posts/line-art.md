---
title: "Line art"
date: 2017-07-12
tags: [ "typescript" ]
---

Built a line drawing tool inspired by [linify.me](http://linify.me/).

Made with a "pin spacing" option so that I can do this for real using cotton string and a wooden frame with pins. Bought the materials but need to update the code to output sequences of directions before attempting it. Initially written using a genetic algorithm, it worked but was waaaay too slow, so switched to subtracting the darkest lines between pins from the image.

<script>
function go() {
	generate(
        document.getElementById("lineDrawing"),
        document.getElementById("original"),
        document.getElementById("pinSpacing").value,
        document.getElementById("numLines").value,
        document.getElementById("darkness").value,
		"/images/baby.png"
    );
}
window.onload = function(e){ 
	go();
}
</script>
<form>
	<table>
    <tr><td>Pin spacing:</td><td><input type="text" name="pinSpacing" id="pinSpacing" value="4"></td></tr>
    <tr><td>Lines: </td><td><input type="text" name="numLines" id="numLines" value="1600"></td></tr>
    <tr><td>Darkness: </td><td><input type="text" name="darkness" id="darkness" value="0.1"></td></tr>
    <tr><td><input type="button" value="Generate" onclick='go()' /></tr></td>
	</table>
</form>
<canvas id="original" width="300" height="300"></canvas>
<canvas id="lineDrawing" width="300" height="300"></canvas>
<script src="/frameart/worker.js"></script>
<script src="/frameart/main.js"></script>
