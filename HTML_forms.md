# Cheat Sheet: Interactive Form Elements

When filling in HTML forms, we expect users to abide by certain rules, like using numbers when asked to, or properly formatting a URL or an email when needed. However, humans are prone to errors and in some cases, they may overlook some of the data they input. That’s why it’s important to ensure the shape of the data we expect in each field is correct. HTML form validation is a set of attributes we can add to form inputs to perform automatic validation on the user’s behalf.

## Most Important Validation Attributes

### Required

Denotes a mandatory input that the user can’t leave empty. It can be used with any input type, like password, radio, text, and so on.

- **Example:**
  ```markdown
  <input type="text" id="firstName" name="firstName" required>

### Maxlength
Specifies the maximum length of a text input, in other words, the maximum number of characters that can be entered for a specific field. If provided, it will prevent the user from entering more characters than the limit.

- **Example:**
  ```markdown
<input type="text" id="description" name="description" maxlength="50">

### Minlength
Specifies the minimum length of a text input. If set, the input will not accept fewer characters than those specified.

- **Example:**
  ```markdown
  <input type="password" id="password" name="password" minlength="8">

### Min and Max Attributes
Determine the minimum and maximum values allowed for an input field. They are usually applied to numerical text inputs, range inputs, or dates.

- **Example (MIN):**
  ```markdown
  <input type="number" id="quantity" name="quantity" min="1">
- **Example (MAX):**
  ```markdown
  <input type="number" id="quantity" name="quantity" max="10">

### Multiple
Indicates that the user can enter more than one value in a single input field. This attribute can only be used for email and file input types.

- **Example:**
  ```markdown
  <input type="file" id="gallery" name="gallery" multiple>

### Pattern
Defines a particular pattern that an input field value has to fulfill to be considered valid. This attribute expects a regular expression to specify the pattern. It works with text, date, search, URL, tel, email, and password input types. For example, you can restrict phone numbers to be only from the UK.

- **Example:**
  ```markdown
  <input type="tel" id="phone" name="phone" pattern="^(?:0|\+?44)(?:\d\s?){9,10}$">
