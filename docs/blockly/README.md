# Blockly Experiment 

<script src="../jquery.min.js"></script>
<script src="../qrcodeborder.js"></script>
<script src="../html2canvas.min.js"></script>
<script src="https://unpkg.com/blockly@latest/blockly_compressed.js"></script>
<script src="https://unpkg.com/blockly@latest/blocks_compressed.js"></script>
<script src="https://unpkg.com/blockly@latest/javascript_compressed.js"></script>
<script src="print_block.js"></script>
<script src="if_then_block.js"></script>

  <style>
    #toolbox {
      display: inline-block;
      width: 100%;
      height: 150px;
      border: 1px solid #ddd;
      vertical-align: top;
      overflow: auto;
    }
    #blocklyDiv {
      height: 480px;
      width: 100%;
      border: 1px solid #ddd;
      display: inline-block;
      vertical-align: top;
    }	
    #output {
      width: 100%;
      height: 100px;
      border: 1px solid #ddd;
      margin-top: 10px;
      white-space: pre-wrap;
      overflow: auto;
    }
  </style>
  
  <div id="content">
    <div id="toolbox">
      <xml id="toolboxXml">
        <block type="basic_print"></block>
        <block type="basic_if_then"></block>
      </xml>
    </div>
    <div id="blocklyDiv"></div>
  </div>
  <button onclick="generateBASICCode()">Generate BASIC Code</button>
  <pre id="output"></pre>
  <script>
    var toolbox = document.getElementById("toolboxXml");
    document.getElementById("toolbox").appendChild(toolbox);

    var workspace = Blockly.inject('blocklyDiv', {toolbox: document.getElementById('toolbox')});
    Blockly.JavaScript.addReservedWords('output');

    function generateBASICCode() {
      var output = document.getElementById('output');
      var code = Blockly.JavaScript.workspaceToCode(workspace);
      output.textContent = code;
    }
  </script>
 
	<div id="qrcode_txt" style="width: 360px">
	 <center>
	  <div id="qrcode"></div><br>
	  <b><font color="#009FDF">GoProQR:</font></b> <em id="qrtext"></em><br>
	  <b><font color="#005CAC">Maximum Shutter Angle</font></b>
	 </center>
	</div>
	<button id="copyImg">Copy Image to Clipboard</button>
	<br>
	<br>
	Share this QR Code as a URL: <small id="urltext"></small><br>
	<button id="copyBtn">Copy URL to Clipboard</button>
        
## Background

Stuff here

**Compatibility:** Labs enabled HHERO11 & MAX 
        
## ver 0.1
updated: March 30, 2023

[BACK](..)

<script>
var once = true;
var qrcode;
var cmd = "mVr4";
var clipcopy = "";
var lasttimecmd = "";
var changed = true;

function dcmd(cmd, id) {
    var x;
	if(document.getElementById(id) !== null)
	{
		x = document.getElementById(id).checked;
		if( x === true)
			cmd = cmd + document.getElementById(id).value;
	}
	else
	{
	    var i;
		for (i = 1; i < 15; i++) { 
			var newid = id+i;
			if(document.getElementById(newid) !== null)
			{
				x = document.getElementById(newid).checked;
				if( x === true)
					cmd = cmd + document.getElementById(newid).value;
			}
		}
	}
	return cmd;
}

function makeQR() 
{	
  if(once === true)
  {
    qrcode = new QRCode(document.getElementById("qrcode"), 
    {
      text : "!oMBURN=\"\"",
      width : 360,
      height : 360,
      correctLevel : QRCode.CorrectLevel.M
    });
    once = false;
  }
}

function checkTime(i) {
    if (i < 10) {i = "0" + i;}  // add zero in front of numbers < 10
    return i;
}

function timeLoop()
{
  var type = "o";
	  
  cmd = dcmd("mVt","iso"); //iso
  cmd = cmd + type + dcmd("","shut"); //shutter angle
  
  qrcode.clear(); 
  qrcode.makeCode(cmd);
  
  if(cmd != lasttimecmd)
  {
	changed = true;
	lasttimecmd = cmd;
  }
	
  if(changed === true)
  {
	document.getElementById("qrtext").innerHTML = cmd;
	clipcopy = "https://gopro.github.io/labs/control/set/?cmd=" + cmd + "&title=Maximum%20Shutter%20Angle";
	document.getElementById("urltext").innerHTML = clipcopy;
	changed = false;
  }
	
  var t = setTimeout(timeLoop, 50);
}

function myReloadFunction() {
  location.reload();
}


async function copyImageToClipboard() {
    html2canvas(document.querySelector("#qrcode_txt")).then(canvas => canvas.toBlob(blob => navigator.clipboard.write([new ClipboardItem({'image/png': blob})])));
}
async function copyTextToClipboard(text) {
	try {
		await navigator.clipboard.writeText(text);
	} catch(err) {
		alert('Error in copying text: ', err);
	}
}

function setupButtons() {	
    document.getElementById("copyBtn").onclick = function() { 
        copyTextToClipboard(clipcopy);
	};
    document.getElementById("copyImg").onclick = function() { 
        copyImageToClipboard();
	};
}

makeQR();
setupButtons();
timeLoop();


</script>
