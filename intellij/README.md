## Intellij - Cheatsheet

__⌘__ = Cmd; __⌥__ = Alt; __⇧__ = Shift; __⌃__ = Ctrl  <br/>

### View / Windows 
<table width="100%">
    <tr>
        <td width="15%">⇧ ⌘ F12</td>
        <td>maximize editor-window <a href="hhttps://www.youtube.com/watch_popup?v=lokcNBCxe9A">show me</a></td>
    </tr>
    <tr>
        <td>⇧ F4</td>
        <td>release the editor-window (in order to move the window to a second screen) <a href="https://www.youtube.com/watch_popup?v=SIBPdwPuWBI">show me</a></td>
    </tr>
</table>

<table width="100%">
    <tr>
        <td width="15%">⌘ 1</td>
        <td>Open Project-Structure</td>
    </tr>
    <tr>
        <td>⌘ 3</td>
        <td>Open (last) Search-Result View</td>
    </tr>
    <tr>
        <td>⌘ 4</td>
        <td>Open Running-Console (Log-Output etc.)</td>
    </tr>
    <tr>
        <td>⌘ 5</td>
        <td>Open Debugging-Console (Breakpoints, Step-In/-Out/-Over etc.)</td>
    </tr>        
    <tr>
        <td>⌘ 7</td>
        <td>Open File Structure <i>(Preferable use <b>⌘ F12</b> to get a structure popup)</i></td>
    </tr>
    <tr>
        <td>⌘ 8</td>
        <td>Open Maven-Project Structure <i>(This one is customized)</i></td>
    </tr>    
    <tr>
        <td>⌘ 9</td>
        <td>Open the Changes View (Loca Changes / Incoming Changes / Repository View)</td>
    </tr>
    <tr>
        <td>⌥ F12</td>
        <td>Open Terminal View</td>
    </tr>    
</table>

### Navigation
<table width="100%">
	<tr>
		<td width="15%">⌘ ⇧ ENTER</td>
		<td>Complete Current Statement - moves the cursor into the brakets of the current stetement</td>
	</tr>
	<tr>
		<td>F2 / ⇧ F2</td>
		<td>jump to the next / preview error (or warning - depends, click at the right yellow/red square to adjust)</td>
	</tr>
	<tr>
		<td>⇧ ⌘ F7</td>
		<td>highlights all references within the current file (also works with `return` and `throws`)</td>
	</tr>
	<tr>
		<td>⌥ F7</td>
		<td>find usages within the whole project</td>
	</tr>
	<tr>
		<td>⌘ L</td>
		<td>shows the navigation bar (<i> Caution: adjusted for my purposes - is not the default</i>)</td>
	</tr>
	<tr>
		<td>⌘ E</td>
		<td>shows all recently edited files (makes tabs obsolete - if you want)</td>
	</tr>
	<tr>
		<td valign="top">⇧ ⌘ N <br/>⌘ N <br/>⌥ ⇧ ⌘ N <br/></td>
		<td>
			Open File (all kind of) /<br/> Open Class (also inner classes) /<br/> Open Symbol (Methods, Members ... everyting)
			<table width="100%">
				<thead><tr><th>Dialog-Input</th><th>Description</th></tr></thead>			
				<tr><td>OST:40</td><td>Opens File/Class matching OST at line 40</td></tr>
				<tr><td>*Subscription</td><td>Opens File/Class containing the word Subscription</td></tr>
				<tr><td>/doc</td><td>Navigate directly to Folder /doc of project</td></tr>
			</table>
		</td>
	</tr>	
	<tr>
		<td>⇧ ⌘ I</td>
		<td>Open Quick Definition - works in all kind of Windows and Popups (i.e. like <b>⌘ F12</b>-Popup)</td>
	</tr>	
</table>

### Code / Refactorings
<table width="100%">
	<tr>
		<td width="15%">⌘ ⌥ M</td>
		<td>Extract into Method - the current selected Code will be encapsulated into a method</td>
	</tr>
	<tr>
		<td>⌘ ⌥ V</td>
		<td>introduce variable - this also works with new-declarations (i.e. new ArrayList<String>() - ⌘ ⌥ V)</td>
	</tr>
	<tr>
		<td>⌥ G</td>
		<td>select next occurence of word (useful for multi-carret editing) <a href="https://www.youtube.com/watch_popup?v=5FPmNJ_KDtY";>show me</a></td>
	</tr>
	<tr>
		<td>⌥ ⌘ L</td>
		<td>Reformat code (optimize Imports as well etc.) <a href="https://www.youtube.com/watch_popup?v=y_4cL7yJNfw";>show me</a></td>
	</tr>
    <tr>
        <td>⇧ ⌥ ⌘ T</td>
        <td>shows all possible  refactorings at this place (context sensitive) - hudge time-saver</td>
    </tr>
    <tr>
        <td>⇧ ⌘ T</td>
        <td>Choose (and subsequently jump to) test for this class (or create one) <a href="https://www.youtube.com/watch_popup?v=KfcszD8f6KU";>show me</a></td>
    </tr>
    <tr>
        <td>⇧ ⌃ J</td>
        <td>Join lines (same as ⌃ J in vi) <a href="https://www.youtube.com/watch_popup?v=Z96eemZwBbg";>show me</a></td>
    </tr>
    <tr>
        <td>⇧ ⌃ U</td>
        <td>toggles case (all to upper- or lower-case)</td>
    </tr>
</table>	

### Usefull `⇧ ⌘ A` commands
<table width="100%">
    <tr>
        <td width="15%">move right</td>
        <td>Splits the screen and moves the current editor-window to the right (if there are more than one tab) <a href="https://www.youtube.com/watch_popup?v=52FkA4moPlM";>show me</a></td>
    </tr>
    <tr>
        <td>column selection mode</td>
        <td>turn to column selection (there's a short-cut for that - but not applicable on my macbook)</td>
    </tr>
</table>
