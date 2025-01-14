# convert-object-selectors-in-tests

Replace object selectors in `assert.dom`, `find`, `click` or `fillIn` with their corresponding string values.

## Usage

```
npx ember-test-convert-object-selectors-codemod convert-object-selectors-in-tests path/of/files/ or/some**/*glob.js

# or

yarn global add ember-test-convert-object-selectors-codemod
ember-test-convert-object-selectors-codemod convert-object-selectors-in-tests path/of/files/ or/some**/*glob.js
```

## Limitations

* Does not transform imported object selectors, e.g. `import SELECTORS from 'test-helpers/test-selectors'`
* Does not transform objects defined on this context, e.g. `assert.dom(this.SELECTOR.IMAGE)`
* Does not remove unused object definitions after transform

## Input / Output

<!--FIXTURES_TOC_START-->
* [convert-object-selectors](#convert-object-selectors)
<!--FIXTURES_TOC_END-->

<!--FIXTURES_CONTENT_START-->
---
<a id="convert-object-selectors">**convert-object-selectors**</a>

**Input** (<small>[convert-object-selectors.input.js](transforms/convert-object-selectors-in-tests/__testfixtures__/convert-object-selectors.input.js)</small>):
```js
import { module, test } from 'qunit';
import { render, find, click, fillIn } from '@ember/test-helpers';
import { IMPORTED_CONSTS } from 'fake-location';

module('foo', function() {

  const SELECTORS = {
    block: '[data-test-block]',
    image: '[data-test-image]',
  };

  const constantSelector = '[data-test-a-cool-selector]';

  const NESTED_SELECTORS = {
    WITH: {
      CONTAINER: '[data-test-container]',
      BUTTON: '[data-test-button]',
    },
  };

  test('assert dom test 1', async function(assert) {
    assert.expect(1);

    assert.dom(SELECTORS.block).exists();
  });

  test('assert dom test 2', async function(assert) {
    assert.expect(2);

    assert.dom(NESTED_SELECTORS.WITH.CONTAINER).exists();
    assert.dom(NESTED_SELECTORS.WITH.BUTTON).exists();
  });

  test('find test 1', async function(assert) {
    assert.expect(1);

    assert.notOk(find(SELECTORS.block));
  });

  test('find test 2', async function(assert) {
    assert.expect(2);

    assert.ok(find(NESTED_SELECTORS.WITH.CONTAINER));
    assert.notOk(find(NESTED_SELECTORS.WITH.BUTTON));
  });

  test('click test', async function(assert) {
    assert.expect(1);

    await click(NESTED_SELECTORS.WITH.CONTAINER);

    assert.dom(NESTED_SELECTORS.WITH.BUTTON).exists();
  });

  test('fillIn test', async function(assert) {
    assert.expect(1);

    await fillIn(SELECTORS.block, 'foo');

    assert.dom(SELECTORS.image).exists();
  });

  test('constant selector test', async function(assert) {
    assert.expect(1);

    assert.notOk(find(constantSelector));
  });

  test('constant within template literal test', async function(assert) {
    assert.expect(1);

    assert.dom(`${constantSelector} [data-test-nested]`).exists();
    assert.ok(find(`${NESTED_SELECTORS.WITH.BUTTON} [data-test-nested]`));
    assert.ok(find(`[data-test-nested] ${NESTED_SELECTORS.WITH.BUTTON}`));
    assert.ok(find(`[data-test-nested] ${NESTED_SELECTORS.WITH.BUTTON} [data-test-nested] ${constantSelector}`));
  });

  test('unresolveable template literal test', async function(assert) {
    assert.expect(1);

    assert.dom(`${IMPORTED_CONSTS.module} [data-test-nested]`).exists();
  });
});

```

**Output** (<small>[convert-object-selectors.output.js](transforms/convert-object-selectors-in-tests/__testfixtures__/convert-object-selectors.output.js)</small>):
```js
import { module, test } from 'qunit';
import { render, find, click, fillIn } from '@ember/test-helpers';
import { IMPORTED_CONSTS } from 'fake-location';

module('foo', function() {

  const SELECTORS = {
    block: '[data-test-block]',
    image: '[data-test-image]',
  };

  const constantSelector = '[data-test-a-cool-selector]';

  const NESTED_SELECTORS = {
    WITH: {
      CONTAINER: '[data-test-container]',
      BUTTON: '[data-test-button]',
    },
  };

  test('assert dom test 1', async function(assert) {
    assert.expect(1);

    assert.dom('[data-test-block]').exists();
  });

  test('assert dom test 2', async function(assert) {
    assert.expect(2);

    assert.dom('[data-test-container]').exists();
    assert.dom('[data-test-button]').exists();
  });

  test('find test 1', async function(assert) {
    assert.expect(1);

    assert.notOk(find('[data-test-block]'));
  });

  test('find test 2', async function(assert) {
    assert.expect(2);

    assert.ok(find('[data-test-container]'));
    assert.notOk(find('[data-test-button]'));
  });

  test('click test', async function(assert) {
    assert.expect(1);

    await click('[data-test-container]');

    assert.dom('[data-test-button]').exists();
  });

  test('fillIn test', async function(assert) {
    assert.expect(1);

    await fillIn('[data-test-block]', 'foo');

    assert.dom('[data-test-image]').exists();
  });

  test('constant selector test', async function(assert) {
    assert.expect(1);

    assert.notOk(find('[data-test-a-cool-selector]'));
  });

  test('constant within template literal test', async function(assert) {
    assert.expect(1);

    assert.dom('[data-test-a-cool-selector] [data-test-nested]').exists();
    assert.ok(find('[data-test-button] [data-test-nested]'));
    assert.ok(find('[data-test-nested] [data-test-button]'));
    assert.ok(find('[data-test-nested] [data-test-button] [data-test-nested] [data-test-a-cool-selector]'));
  });

  test('unresolveable template literal test', async function(assert) {
    assert.expect(1);

    assert.dom(`${IMPORTED_CONSTS.module} [data-test-nested]`).exists();
  });
});

```
<!--FIXTURES_CONTENT_END-->
