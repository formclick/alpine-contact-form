# Alpine.js contact form

A contact form built with Alpine.js that sends submissions with the [Formclick](https://www.formclick.io) form API. One HTML file, no build step, no backend: reactive loading, error and thank-you states in a few lines, and every message lands in your inbox and your Formclick dashboard.

## Quick start

1. Create a free account at [formclick.io](https://www.formclick.io), create a form and copy its **access key**.
2. Open [`index.html`](index.html) and set `ACCESS_KEY` in the script at the bottom.
3. Open the page in a browser (or `npx serve .`) and send a test message.

## How it works

```html
<section x-data="contactForm()">
  <form @submit.prevent="submit">
    <input name="email" x-model="fields.email" required>
    <textarea name="message" x-model="fields.message" required></textarea>
    <button :disabled="status === 'sending'">Send message</button>
    <p x-show="status === 'error'" x-text="error"></p>
  </form>
</section>

<script>
  function contactForm() {
    return {
      fields: { name: '', email: '', message: '', _honeypot: '' },
      status: 'idle',
      async submit() {
        const res = await fetch('https://api.formclick.io/submit', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json', Accept: 'application/json' },
          body: JSON.stringify({ ...this.fields, access_key: ACCESS_KEY }),
        });
      },
    };
  }
</script>
```

| Piece | Why it's there |
| --- | --- |
| `x-data="contactForm()"` | Holds the form values and status in one Alpine component. |
| `x-model` | Two-way binds each input to `fields`, which is sent as JSON. |
| `@submit.prevent` | Stops the browser's normal post so the page doesn't reload. |
| `x-show` / `x-text` / `:disabled` | Show the error, the `Sending…` label and the thank-you screen from `status`. |
| `access_key` | Identifies your form. It can only create submissions, so it's safe in public code. |
| `_honeypot` | A hidden spam trap. Bots fill it and those submissions are silently discarded. |

Alpine.js 3.17.5 is loaded from jsDelivr with a pinned version.

## Responses

| Status | Meaning |
| --- | --- |
| `200` | Saved and delivered. |
| `400` | Missing or invalid access key, empty submission, or a failed captcha. |
| `403` | Form disabled, website not on the form's allowed domains, or monthly limit reached. |
| `404` | No form exists for this access key. |
| `429` | Too many submissions from one visitor in a short time. |

## Learn more

- [Form API reference](https://www.formclick.io/form-api)
- [All Formclick examples](https://github.com/formclick/contact-form-examples)

## License

[MIT](LICENSE)
