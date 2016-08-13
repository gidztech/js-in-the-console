I recently came across a [question](http://stackoverflow.com/questions/38930923/how-to-find-out-colour-of-placeholder/38931792#38931792) 
on Stack Overflow about viewing the CSS styles of a `placeholder` attribute in Chrome DevTools. 
I worked out that because it is a pseudo element, it doesn't show up in the Styles/Computed panels. The only way to view the styles is
to check them manually in the stylesheet or within inline `style` tag.

I discovered that you could access the stylesheets in JavaScript, 
using the [`document.styleSheets`](https://developer.mozilla.org/en-US/docs/Web/API/Document/styleSheets) property. This gave me the idea
to come up with a function you could run as a snippet in the Console, in which you specify the selector (or part of the selector), and
the attribute you are looking for.

**Note to self:** Extend this to take an array of attributes

```javascript
function getSelectorStyle(selector, attribute) {

    var rules = [];

    function CSSRule(sheet, selector, value) {
        this.sheet = sheet;
        this.selector = selector;
        this.value = value;
    }

    var styleSheets = document.styleSheets;
    for (var i = 0; i < styleSheets.length; i++) {
        var currentSheet = document.styleSheets[i];
        if (currentSheet != null) {
            var ruleList = currentSheet.cssRules;
            if (ruleList != null) {
                for (var j = 0; j < ruleList.length; j++) {
                    var currentRule = ruleList[j];
                    if (currentRule.selectorText != null) {
                        if (currentRule.selectorText.indexOf(selector) != -1) {
                            var sheetLocation = currentSheet.href ? currentSheet.href : "inline-css";
                            var item = new CSSRule(sheetLocation, currentRule.selectorText, currentRule.style[attribute]);
                            rules.push(item);
                        }
                    }
                }
            }
        }
    }

    console.table(rules)
}

getSelectorStyle("placeholder", "color");
```
![Example for Placeholder][example]

Now I can quickly find out the placeholder style.

[example]: http://i.imgur.com/LyWMIc0.png "Example with Placeholder"

