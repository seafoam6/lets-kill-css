# Let's kill <span class="gold">CSS</span>
<br>
<br>
<span class="byline">By Steve Barman, lover of CSS and JS</span>

---

> Two css properties walk into a bar. A barstool in a completely different bar falls over. 
> - [@thomasfuchs](https://twitter.com/thomasfuchs)

---

# Problems with css
- CSS requires trust |
- CSS is open to interpretation |
- everyone on your team needs to be dedicated to CSS or it falls apart |
- reorganizing parts of your page can break all of your styles |
- too many mental models

note: (or you have that one person who is really into CSS and hates all their coworkers) 

---

# CSS
Stands for "Cascading Style Sheets"

---

### The Cascade

A CSS primer for you: 
- A class can be used to style an element. 
- if there are two contradictory css rules for that class, the css class that's further down the page will win. 
- two classes will beat one class
- an id will beat a class
- an important will beat a class
- an inline style will beat that class
- if there's multiple class names on the same element, the last classname will win (assuming the same specificity)

---

### Go read someone else's css

If you open a website, reading the CSS from top to bottom, you'll be reading a historic record of the css from begining to end. Typically at the begining you'll see clean intelligent focused styles. 

---

in the beginning, line 14: 

```
.dashboard-announcement {
    box-sizing: border-box;
    overflow: hidden;
    opacity: 1;
    transition: max-height .75s ease, opacity .75s ease
}
```

---

near the end, line 25843:

```
[data-help-popover=inline-controls].animate-inline-controls-tutorial-original .inline-controls .tray:not(:hover) .control.add-read-more {
    animation-delay: 2s
}

```

---

## Position matters

```
button.giant_blue_button:hover .blue_button .blue_button_right {
    background-position: -960px -100px
}
```
Cool, so this "blue button right" will have that background position, when it's inside an element with the class `blue_button` inside a button with the class `giant_blue_button` that's actively being hovered.

What about if we want to move `blue_button_right` ? 
1) rewrite your css
2) maybe give it an `id`, but then you'll have to overpower it later

---

## Naming convention matters

```
button.giant_blue_button:hover .blue_button .blue_button_right {
    background-position: -960px -100px
}
```

Someone at Tumblr convinced their team there would be something called `blue_button` and something called `blue_button_right`. 

note: I've worked on similar projects, where by the end, the client had us move `blue_button_right` to the left, but no one wanted to search/replace all their CSS, rename everything and repair all the broken css. So `blue_button_right` just happened to live to the left of `blue_button_left`

---

# JavaScript to the Rescue
If you have the full power of JavaScript, why let the limitations of CSS get in your way?
---

### Forget the cascade with Object.assign()

---

Put your stuff where ever you want

--- 

### Use javascript to get rid of naming conflicts, scoped styles

---

### styles in JavaScript

in CSS:
```
.internal-link{
  border-bottom: '3px solid #f7d71e',
  font-style: 'italic'
}
```
note: here's a pretty standard bunch of css
---

in: JS
```
const internalLink = {
  borderBottom: '3px solid #f7d71e',
  fontStyle: 'italic'
}
```
note: in JS, we just need to convert any kabob-case into camel case. This is just a Plain Ol JavaScript Object and can be treated as such. 

---

### let's do it

```
const NextLink = ({ link }) => (
  <Link href={link.link}>
    <a className="f6 f5-l link bg-animate black-80 hover-bg-light-yellow dib pa3 ph4-l">{link.name}</a>
  </Link>
)
```
note: this is from our DetroitJS website. This block deals with navigation links that go to other routed pages, rather than external links. Let's say we want to add our POJO of styles to just this block. We could add yet another CSS class to the list, we could find out what border bottom, and italic are. Let's be lazy.

---

The easy way.

```
import internalLink from "../../styles.js"

const NextLink = ({ link }) => (
  <Link href={link.link}>
    <a className="f6 f5-l link bg-animate black-80 hover-bg-light-yellow dib pa3 ph4-l" style={internalLink}>{link.name}</a>
  </Link>
)
```
note: now there's no worrying that some other class is going to mess our style up. We're hitting the styles at the root, in the element. 
---

# Combine styles.

const superStyle = Object.assign(internalLink, blueStuff, sunnyD, purpleStuff);

note: what if we have a bunch of javaScript style objects we need to add to an element? Just treat them like javaScript objects and you'll be fine. Any properties later in the chain will supercede properties earlier in the chain. 

---

# Send css packing

No matter where we move this `JSX` block, as long as we have this style block attached, the styles will render as we expect them to. 

---

# Other flavors

- styled-components | 
- jsxstyle |
- glamouous |
- css-modules |


note: let's see how other Styles-in-js libraries work
---

### Styled-components

> "Best Practices? I don't give a shit. I need to get this done by tomorrow." 
> - Max Stoiber

note: Styled-components keeps everything componentized, just like React!

---
minor alteration to pass down `className`

```
const NextLink = ({ link, className }) => (
  <Link href={link.link}>
    <a className={`f6 f5-l link bg-animate black-80 hover-bg-light-yellow dib pa3 ph4-l ${className}`}>{link.name}</a>
  </Link>
)
```

---

```
const BlueLink = styled(NextLink)`
color: blue;
font-weight: bold;
  &:hover{
    background: DarkSeaGreen
  }
`
// later in the file
render = () => <BlueLink link={link} key={shortid.generate()} />
```
note: now anywhere that I would have used the NextLink, I can Use the BlueLink the exact same way. What this looks like in the dom, is there will be a generated class name that's mapped to a generated style tag. 

---
Modify styles based on props. 

```
const BlueLink = styled(NextLink)`
color: blue;
font-weight: bold;
background: ${props => props.primary ? 'palevioletred' : 'white'};
  &:hover{
    background: DarkSeaGreen
  }
`
// later in the file
render = () => <BlueLink primary link={link} key={shortid.generate()} />
```
note: there's also a themeing system built in! Passing in a theme prop, can modify your themes. Arguments to the styles can also be passed in as props. 
---
### JSXStyle

```
  <Block
    backgroundColor="#EEE"
    boxShadow="inset 0 0 0 1px rgba(0,0,0,0.15)"
    borderRadius={5}
    height={64}
    width={64}
    marginRight={15}
    backgroundSize="contain"
    backgroundImage="url(http://graph.facebook.com/justinbieber/picture?type=large)"
  />
```
note: This saves you the time of naming classes, because there aren't any classes to name. Don't jump between CSS and JS files. 

---

### GLamorous

```
const MyStyledDiv = glamorous.div({
  fontSize: 20,
  textAlign: 'center',
})

// later

<myStyledDiv>Wow a Div!</myStyledDiv>
```
note: glamorous is very much like styled components, except it requires you to write with JS style objects. You can also override a components style by passing in styles to the CSS prop, as well as overriding it's childrens styles. 

---
### CSS Modules

```
import React from 'react';
import styles from './DashedBox.css';

const DashedBox = () => (
  <div className={styles.container}>
    <p className={styles.content}>Get started with CSS Modules style</p>
  </div>
);

export default DashedBox;

```
note: I kind of hate this because it's the worst of both worlds. The CSS is scoped to the component though! camelcase only!

---

# Questions, Comments?