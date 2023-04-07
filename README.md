# taskify

A [`markdown-it`](https://github.com/markdown-it/markdown-it) plugin to add support for Github style task lists with feedback.

## Installation

```bash
npm i @fordi-org/taskify
```

## Usage

Setting up the renderer and rendering content is easy:

```javascript
import Markdown from 'markdown-it';
import taskify from '@fordi-org/taskify';

// Optional argument; see docs below
const options = {
  listClass: 'has-tasks',
  itemClass: 'task',
  labelClass: 'task--label',
  checkboxClass: 'task--check',
  readOnly: false,
};

const md = Markdown().use(taskify, options);

const htmlString = parser.render(markdownString);
```

### options

Task lists are structured as follows:

```html
<ul class={listClass}>
  <!--...-->
  <li class={itemClass}>
    <label class={labelClass}>
      <input type="checkbox" class={checkboxClass} data-pos="..." disabled={readOnly} />
      <!-- ... remainder of item content ... -->
    </label>
  </li>
  <!--...-->
</ul>
```

| name          | type    | default   | meaning 
|:--------------|:--------|:----------|:-------
| listClass     | string  | undefined | class name for the list if there are tasks on it
| itemClass     | string  | undefined | class name for an item containing a task
| labelClass    | string  | undefined | class name for the label of the task
| checkboxClass | string  | undefined | class name for the task's checkbox
| readOnly      | boolean | true      | whether the checkboxes should be read-only

Once rendered, each checkbox will have a `data-pos` attribute, which is the index from the source
content where the `x` or ` ` is.  So an example check handler would be:

```javascript
const spliceString = (text, size, inserted) => [
  text.substring(0, pos),
  inserted,
  text.substring(pos + size),
].join('');

const onCheckClick = ({ target }) => {
  // grab the source position off the checkbox
  const pos = parseInt(target.getAttribute('data-pos'));
  // Replace the check at that position
  const newContent = spliceString(content, pos, target.checked ? 'x' : ' '));
  // Commit the new content - implementation will be app specific
  saveAndRender(newContent);
};
```
