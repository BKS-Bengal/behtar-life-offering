# Headless Chrome --window-size clipped a nav that actually fit

## the problem
A 390px screenshot of the Behtar Life offering showed Night and Menu as a one-pixel sliver. Playwright at a real 390 CSS-pixel viewport showed both buttons fully inside the bar (Menu right edge at 378).

## the approach
1. Do not trust `chrome --headless --window-size=390,844 --screenshot`. On this Windows machine that size is device pixels. Display scaling shrinks the CSS viewport, so a bar that fits at 390 CSS pixels overflows the shot.
2. Measure with Playwright pointed at the installed Chrome: `executable_path=r"C:\Program Files\Google\Chrome\Application\chrome.exe"` and `page.set_viewport_size({"width": 390, "height": 844})`.
3. Read `getBoundingClientRect()` for `.brand`, `#theme`, and `#menu`, and compare `documentElement.scrollWidth` to `innerWidth`. At 320, 360, 390, and 414 the scroll width matched the viewport and Menu's right edge stayed inside it.
4. Confirm by `page.screenshot` from that same Playwright page, not from `--window-size`.

## the judgment calls
Moving Night into the menu was the planned fix if the buttons truly overflowed. The rectangles showed they do not, so the nav markup stayed as it is. Shrinking the logo further would have been a fix for the bad screenshot.

## the reusable rule
On this PC, judge mobile layout from a Playwright CSS viewport, not from `chrome --window-size`.
