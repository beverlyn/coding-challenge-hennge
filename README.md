## Description
For this challenge, we were provided with the base code for an email auditing system. One of the component files was left blank for us to implement our solution.

More details about the coding chalenge are provided under 'Additional Info'.

## Solution
My solution to the coding challenge is implemented in [RecipientsDisplay.vue](https://github.com/beverlyn/coding-challenge-hennge/blob/main/src/components/RecipientsDisplay.vue)

Usually, for cleaner code, I'd include the minimum amount of comments to explain certain parts. I also try to implement self documenting code.

But for the purpose of this challenge, I added many comments to thoroughly explain my thought procress, and to provide detailed documentation.

## Additional Info
The specifics and rules of the coding challenge are provided below.
<details><summary>CHALLENGE.md</summary>
<p>

# Challenge Details

Development of an `Email Audit` system is currently in progress. Assume that this project is a real project and is being used in a production environment on a trial basis, since the features are not yet complete.

As the first assignment, two UI elements need to be implemented:

- The first one is called `RecipientsDisplay`, it is a table cell that is used to intelligently show email recipients inside the `AuditTable` component. This cell will trim recipients that are too long to display in the table.
- The second element is called `RecipientTooltip`, a tooltip-like component that will be used to display all of the email recipients, even ones that are trimmed in the table cell.

The task is to implement these UI elements in the `RecipientsDisplay` file within any of the included frameworks. Modifying or adding new props, re-ordering the recipients, and adding new or extra functionalities and features are not allowed in this assignment. Additionally, the component needs to be written in **Typescript**.

## `RecipientsDisplay`

- If all the recipient email addresses fit in the available space, we can simply display them delimited by a comma and space (e.g. `a, b`).
- When there is not enough space to display all recipients, we trim the text. However, to prevent showing clipped email addresses that are hard to read, we trim entire email addresses. If we cannot fit the entirety of a recipient email address, it shouldn't be shown at all.
- When at least one recipient is trimmed, we put a comma, space, and ellipsis after the last fitting recipient (e.g. `a, b, ...`). Furthermore, the rightmost end of the column should show a badge with the number of trimmed recipients (`+N`). We have provided the badge as a component called `RecipientsBadge` under `src/components`, and it accepts the number of truncated recipients as a prop.
- If there is not enough space to show even the first recipient, the badge should show the number of trimmed recipients excluding the first recipient, and the recipient should be truncated with an ellipsis only. If there is only one recipient, there will be no badge, and the recipient should still be truncated by an ellipsis.
- This functionality should work on any screen size and when the screen is resized. For simplicity, this will only be tested in a recent version of a Chromium browser.
- Do not modify or add new props to the `RecipientsBadge` component.
- Do not re-order the recipients.
- Do not add new/extra functionalities and features.

Assume that an employee can send an email to many recipients. Due to the limited amount of space, the information has to be displayed well. The design team has come up with the following design specifications:

- If all the email addresses in the recipients list fit in the available space, display them as they are, delimited by a comma and space (e.g. `John.Smith@gmail.com, Jane.Smith@outlook.com`).
- If there is not enough space to display the entire recipients list, it must be trimmed. To prevent showing clipped email addresses that are hard to read, show only the portion of the recipients list that does fit. In other words, if the entirety of an email address does not fit, it must not be shown.
- If the recipients list has been trimmed (i.e. at least one email address is not shown), add `, ...` after the last email address shown. Furthermore, the rightmost end of the column must indicate the number of trimmed recipients with the provided `RecipientsBadge` component.
- A special case is given to the first recipient. If there is not enough space to fit even the first recipient's email address, the email address is allowed to be clipped with an ellipsis. If there is only one recipient, a badge must not be shown. If there is more than one recipient, the first recipient must be excluded from the number of trimmed recipients in the badge.
- This functionality should work on any screen size and when the screen is resized. For simplicity, this will only be tested in a recent version of a `Chromium` browser.
- For the element that holds the list of recipients, the `display` must be set to `flex` and the `align-items` property must be set to `center` to ensure alignment correctness.

### `RecipientsDisplay` Examples

**Trim recipients that do not fit in the column. Show `, ...` after the last fitting recipient and a badge with `+N` at the end of the column.**

![Email trim example 1](https://github.com/beverlyn/coding-challenge-hennge/assets/10805478/4c96da9f-295a-410b-8695-bc9b0111e311)

**If there is not enough space to show the ellipsis and the extra space, trim that recipient as well.**

Incorrect:

![Email trim example 2A](https://github.com/beverlyn/coding-challenge-hennge/assets/10805478/ad2632f5-c0ee-4d38-b6e3-81ee43b90b89)

Correct:

![Email trim example 2B](https://github.com/beverlyn/coding-challenge-hennge/assets/10805478/e5728f36-308f-4100-aac5-d6feca3d00e8)

**If there is not enough space to show the first recipient, the badge should show the number of trimmed recipients excluding the first recipient, and the recipient should be truncated with an ellipsis only. If there is only one recipient, there should be no badge.**

Two recipients:

![Email trim example 3A](https://github.com/beverlyn/coding-challenge-hennge/assets/10805478/264efa1a-57b6-403d-a801-b749f04e82c9)

One recipient:

![Email trim example 3B](https://github.com/beverlyn/coding-challenge-hennge/assets/10805478/cb743e36-1bf5-4b49-af01-ad2f0a68f544)

## `RecipientsTooltip`

Assume a use-case exists where the entirety of a recipient list must be made visible. The solution provided by the design team is to show the full list of the recipients at the **top right** corner of the viewport.

- The recipients list must be shown in a tooltip at the **top right** corner of the viewport.
- The tooltip must only be shown when the user hovers over a `RecipientsBadge` component.
- The tooltip must not be shown if the user is not hovering over a badge.
- The tooltip must display all of the email addresses in the recipients list, delimited by a comma and space (e.g. `John.Smith@gmail.com, Jane.Smith@outlook.com`).
- Do not create a new file, the tooltip must be located inside the `RecipientsDisplay` file.
- Do not re-order the recipients, display them as they are.
- Do not add new/extra functionalities and features.
- The tooltip should have the following styles:
  - Margin from the top right corner of the viewport is `8px`.
  - Padding top and bottom are `8px`.
  - Padding left and right are `16px`.
  - Background color is `#666`.
  - Text color is `#f0f0f0`.
  - Border radius is `24px`.
  - The `display` property must be set to `flex` and the `align-items` property must be set to `center` to ensure alignment correctness.

### `RecipientsTooltip` Examples

**An example format of showing the recipients list in the tooltip.**

```bash
a@test.example.com, b@test.example.com, c@test.example.com
```

**The example of margins for the recipients list.**

<img width="1308" alt="Tooltip example 1" src="https://github.com/beverlyn/coding-challenge-hennge/assets/10805478/a839edb6-de11-458f-a112-2b415e635e4c">

**The style example for recipients list in the tooltip.**

<img width="1186" alt="Tooltip example 2" src="https://github.com/beverlyn/coding-challenge-hennge/assets/10805478/69e19375-b420-4232-b1a8-f08f63d70fac">

</p>
</details> 
