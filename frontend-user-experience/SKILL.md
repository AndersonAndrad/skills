---
name: frontend-user-experience
description: Reviews and improves frontend interfaces, forms, accessibility, interactions, validation, masks, loading states, errors, responsiveness, keyboard navigation, visual feedback, and user experience. Use whenever creating or modifying pages, components, forms, buttons, links, inputs, modals, menus, tables, dialogs, or any user-facing interface.
---

# Frontend User Experience

Apply this skill whenever creating or modifying something visible or
interactive in the frontend.

Do not consider a frontend task complete only because the interface renders.

The interface must also be understandable, accessible, predictable,
responsive, and safe to interact with.

## 1. Inspect the existing design

Before modifying the interface:

1. Inspect similar components already used by the project.
2. Reuse the existing design system when available.
3. Reuse existing components, tokens, spacing, typography, and interaction patterns.
4. Avoid introducing a new visual convention without necessity.
5. Verify whether the project already has form, input, button, modal, toast,
   loading, and validation components.

Do not duplicate components that already exist.

## 2. Semantic HTML

Use native semantic elements whenever possible.

Prefer:

- `button` for actions
- `a` for navigation
- `label` associated with form fields
- `form` for form submission
- `nav` for navigation
- `main` for main content
- `header` and `footer` for their respective regions
- headings in a logical hierarchy
- lists for collections of related items

Do not use a generic `div` or `span` as a button when a native `button`
can be used.

If a custom interactive element is unavoidable, it must implement:

- Keyboard interaction
- Appropriate role
- Accessible name
- Focus behavior
- Disabled behavior
- Screen reader semantics

## 3. Clickable elements

Every clickable element must clearly appear interactive.

Verify:

- The correct semantic element is used.
- The element has a visible hover state when hover is supported.
- The element has a visible keyboard focus state.
- The element has an active or pressed state when applicable.
- Disabled elements look disabled.
- Disabled elements cannot be activated.
- The cursor communicates interactivity when appropriate.
- The clickable area is sufficiently large.
- Nested clickable elements are avoided.

Use `cursor: pointer` for custom clickable elements when it improves clarity.

Do not use `cursor: pointer` as a substitute for semantic HTML.

Prefer a native `button` or `a` before adding click behavior to a generic
element.

## 4. Buttons

Every button must have a clear purpose.

Verify:

- The label describes the action.
- Icon-only buttons have an accessible name.
- The button type is explicitly defined inside forms.
- Submit buttons use `type="submit"`.
- Non-submit buttons use `type="button"`.
- Destructive actions are visually and textually distinguishable.
- Critical destructive actions request confirmation when appropriate.
- Loading buttons prevent repeated submissions.
- Disabled buttons communicate why the action is unavailable when necessary.

Buttons should support the applicable states:

- Default
- Hover
- Focus-visible
- Active
- Loading
- Disabled
- Success or completed, when relevant

A hover state should provide noticeable feedback, such as:

- Background color change
- Border color change
- Text color change
- Subtle elevation
- Underline for links
- Small visual movement when consistent with the design system

Do not use an aggressive animation for routine actions.

Do not rely only on color to communicate state.

## 5. Keyboard navigation

All functionality must be usable without a mouse when reasonably applicable.

Verify:

- Interactive elements are reachable using `Tab`.
- Focus order follows the visual and logical order.
- Focus is visible.
- Enter and Space activate the appropriate controls.
- Escape closes dismissible dialogs, menus, and overlays when appropriate.
- Focus does not become trapped unintentionally.
- Modals intentionally trap focus while open.
- Focus returns to the triggering element after closing a modal.
- Hidden elements are not keyboard-focusable.

Do not add positive `tabindex` values such as `tabindex="1"`.

Prefer native browser focus behavior.

## 6. Focus states

Do not remove focus outlines without providing an accessible replacement.

Use `:focus-visible` when supported by the project.

A valid focus state must:

- Be clearly visible
- Have sufficient contrast
- Not depend only on a subtle color variation
- Work on light and dark backgrounds
- Be consistent across interactive components

## 7. Forms

Every form field must have:

- A visible label
- A stable field name
- The correct input type
- The correct autocomplete value when applicable
- Clear required or optional indication
- Validation feedback
- Accessible error association
- Predictable keyboard behavior

Placeholder text must not replace the label.

Use placeholders only for examples or additional hints.

Examples:

- `email` for email fields
- `tel` for telephone fields
- `password` for password fields
- `number` only when numeric increment behavior is appropriate
- `inputmode="numeric"` for numeric text entry that must preserve formatting
- `autocomplete="email"` for email
- `autocomplete="current-password"` for existing passwords
- `autocomplete="new-password"` for password creation
- `autocomplete="one-time-code"` for verification codes

Do not disable autocomplete without a real security or domain reason.

## 8. Input normalization

Normalize input only when the domain allows it.

Before trimming or transforming a value, classify the field.

### Usually safe to trim at the boundaries

Examples:

- Email
- Personal name
- City
- State
- Address fields
- Search query
- Company name
- Simple username, when spaces are not allowed
- Optional short text fields where surrounding spaces have no meaning

Prefer trimming leading and trailing whitespace.

Do not automatically remove meaningful spaces inside a value.

### Do not trim or normalize blindly

Examples:

- Password
- Access token
- Refresh token
- API key
- Cryptographic signature
- Hash
- Encoded value
- Verification code when formatting is meaningful
- File content
- Rich text
- Free-form message
- Secret
- Provider-specific identifier
- Data that must be compared byte-for-byte

Do not mutate a password silently.

If passwords with leading or trailing spaces are prohibited, reject them with
an explicit validation message instead of silently changing them.

### Normalization rules

When normalization is appropriate:

1. Perform it at a clearly defined boundary.
2. Keep frontend and backend behavior consistent.
3. Avoid applying normalization in multiple layers unpredictably.
4. Test the normalized and non-normalized scenarios.
5. Document behavior that affects user input.

Do not change casing unless the field is explicitly case-insensitive.

## 9. Input masks

Use an input mask only when it improves data entry.

A mask must not become the source of truth for validation.

Verify:

- The user can paste values with or without formatting.
- Backspace and Delete behave naturally.
- Selection replacement works.
- Mobile keyboard behavior is appropriate.
- The cursor does not jump unexpectedly.
- Existing values can be edited.
- The raw value can be extracted reliably.
- Screen readers can understand the field.
- The field remains usable without JavaScript when applicable.
- The backend validates the actual domain value.

Common masked fields may include:

- CPF
- CNPJ
- CEP
- Telephone
- Dates
- Currency
- Bank information

Do not use a mask for fields whose format varies significantly between
countries or providers unless the context is restricted.

Store and transmit either the normalized or formatted value according to the
project contract.

Do not assume the formatted value is valid merely because it matches the mask.

## 10. Validation

Validation must help the user correct the problem.

Provide validation:

- At the appropriate time
- Near the affected field
- With a clear and specific message
- Without exposing internal implementation details
- Without deleting the user's input unnecessarily

Avoid showing errors before the user has had a reasonable chance to interact
with the field.

When applicable, validate:

- Required fields
- Minimum and maximum length
- Allowed format
- Allowed values
- Numeric ranges
- Date ranges
- File type and size
- Cross-field rules
- Duplicate values
- Business restrictions

Frontend validation improves experience but does not replace backend validation.

## 11. Error messages

Error messages must explain:

1. What went wrong
2. Which field or action was affected
3. What the user can do next

Prefer:

- `Informe um e-mail válido.`
- `A senha deve conter pelo menos 8 caracteres.`
- `Não foi possível salvar. Revise os campos destacados.`
- `Não conseguimos concluir o pagamento. Tente novamente.`

Avoid:

- `Erro`
- `Inválido`
- `Bad request`
- `500`
- Internal exception messages
- Raw provider errors
- Stack traces
- Technical identifiers without explanation

Preserve useful backend validation messages when they are safe and
understandable.

Map technical errors to user-friendly messages.

## 12. Error accessibility

When a field has an error:

- Apply an error state consistently.
- Associate the error message with the field.
- Use `aria-invalid="true"` when appropriate.
- Use `aria-describedby` to connect the input and error text.
- Do not communicate the error using color alone.
- Move focus to the first invalid field after an unsuccessful submission when
  this improves usability.
- Provide an error summary for large forms when appropriate.

Dynamic global errors should be announced using an appropriate live region.

Do not overuse assertive announcements.

## 13. Loading states

Every asynchronous action must communicate progress when the delay is
noticeable.

Use the appropriate loading pattern:

- Button loading state
- Skeleton
- Spinner
- Progress indicator
- Inline status
- Optimistic update
- Background refresh indicator

During loading:

- Prevent duplicate submissions when needed.
- Preserve user-entered data.
- Avoid unexpected layout shifts.
- Keep the user informed.
- Allow cancellation for long-running operations when practical.

Do not display a full-page loader for a small local action.

## 14. Empty states

Collections and result areas must have intentional empty states.

Differentiate:

- No data exists yet
- No search results were found
- Filters removed all results
- Data failed to load
- The user lacks permission
- The feature is unavailable

An empty state should explain the situation and provide the next useful action
when possible.

Do not render a blank screen.

## 15. Success feedback

Provide confirmation when the result is not visually obvious.

Examples:

- Saved successfully
- Copied to clipboard
- Item removed
- Message sent
- Upload completed
- Settings updated

Avoid success messages when the updated UI already makes the result completely
clear.

Do not use a toast as the only confirmation for a critical operation when the
state must remain visible.

## 16. Destructive actions

For destructive or irreversible actions:

- Use a clear label.
- Explain the consequence.
- Distinguish the action visually.
- Request confirmation when accidental activation would be harmful.
- Prefer undo when practical.
- Prevent repeated execution.
- Preserve context if the operation fails.

Avoid vague labels such as `Confirmar` in destructive dialogs.

Prefer labels such as:

- `Excluir usuário`
- `Cancelar pedido`
- `Remover integração`

## 17. Modals and dialogs

A modal must:

- Have an accessible title.
- Have a clear purpose.
- Move focus inside when opened.
- Keep focus within the modal while open.
- Close through an explicit action.
- Support Escape when dismissal is allowed.
- Return focus to the triggering element.
- Prevent background interaction.
- Avoid losing unsaved user data accidentally.

Do not use a modal when inline content or a dedicated page would be simpler.

## 18. Tables and lists

For data tables:

- Use correct table semantics.
- Provide meaningful column headers.
- Keep actions understandable.
- Avoid hiding essential information only on hover.
- Provide empty, loading, and error states.
- Make horizontal overflow usable on smaller screens.
- Preserve important context on responsive layouts.
- Label icon-only actions.

For large datasets, consider:

- Pagination
- Virtualization
- Filtering
- Sorting
- Search
- Result count
- Loading performance

Do not render an unbounded collection.

## 19. Responsive behavior

Verify the interface at relevant viewport sizes.

Check:

- Text does not overflow.
- Buttons remain reachable.
- Forms remain usable.
- Modals fit the viewport.
- Tables have an intentional mobile strategy.
- Touch targets are sufficiently large.
- Fixed elements do not hide content.
- Virtual keyboard behavior does not block important fields.
- Horizontal scrolling is intentional rather than accidental.

Do not treat desktop shrinking as a complete mobile design.

## 20. Motion and animation

Animations should explain state changes, not delay the user.

Verify:

- Motion is subtle and purposeful.
- Animations do not block interaction.
- Loading animation does not create unnecessary distraction.
- Reduced-motion preferences are respected.
- No essential information depends on animation.
- Repeated animation can stop.

Use `prefers-reduced-motion` when the project supports motion.

## 21. Contrast and color

Verify:

- Text has sufficient contrast.
- Disabled content remains understandable.
- Focus indicators are visible.
- Error and success states do not rely only on color.
- Links are distinguishable from surrounding text.
- Placeholder text remains readable but visually secondary.
- Light and dark themes remain usable when supported.

Do not assume a color is accessible because it looks visually distinct.

## 22. Touch experience

For touch interfaces:

- Use adequately sized touch targets.
- Keep sufficient spacing between destructive and common actions.
- Do not rely only on hover.
- Avoid gestures without visible alternatives.
- Ensure menus and dropdowns remain usable.
- Use appropriate mobile input modes.

Hover can enhance desktop experience but must not be required to discover or
use essential functionality.

## 23. Performance experience

Avoid frontend behavior that makes the interface feel slow.

Review:

- Unnecessary renders
- Large assets
- Layout shifts
- Blocking requests
- Repeated API calls
- Missing request cancellation
- Expensive calculations during input
- Heavy components loaded before needed
- Unbounded lists
- Images without appropriate dimensions

Do not optimize prematurely, but avoid obvious user-facing performance
problems.

## 24. Frontend security

Check:

- User-provided HTML is not rendered unsafely.
- Sensitive values are not logged.
- Tokens are not exposed unnecessarily.
- Error messages do not leak internal data.
- Authorization is not enforced only by hiding UI elements.
- External links use safe behavior when opening a new tab.
- File previews and uploads are handled safely.
- URLs derived from user input are validated.

Hiding a button does not replace backend authorization.

## 25. Tests

Add or update tests for important user behavior.

When applicable, test:

- Form submission
- Validation messages
- Invalid fields
- Keyboard interaction
- Loading states
- Disabled behavior
- Error responses
- Success responses
- Masked input
- Pasted input
- Value normalization
- Modal focus behavior
- Responsive behavior
- Accessible name and role
- Regression scenarios

Prefer tests based on what the user sees and does.

Avoid tests that depend unnecessarily on internal component implementation.

## 26. Final frontend checklist

Before finishing, verify all applicable items.

### Interaction

- [ ] Clickable elements use appropriate semantic elements.
- [ ] Clickable elements have clear visual feedback.
- [ ] Hover states exist when relevant.
- [ ] Focus-visible states exist.
- [ ] Disabled states work correctly.
- [ ] Loading prevents accidental duplicate actions.
- [ ] Icon-only controls have accessible names.

### Forms

- [ ] Inputs have visible labels.
- [ ] Input types and autocomplete values are correct.
- [ ] Masks accept typing, editing, and pasting.
- [ ] Frontend and backend validation are compatible.
- [ ] Error messages are specific and actionable.
- [ ] Errors are associated with their fields.
- [ ] Input normalization is domain-safe.
- [ ] Passwords, tokens, and secrets are not silently trimmed.

### Accessibility

- [ ] The flow is usable with a keyboard.
- [ ] Focus order is logical.
- [ ] Focus is visible.
- [ ] Colors have adequate contrast.
- [ ] Information does not depend only on color.
- [ ] Dynamic feedback is announced when appropriate.
- [ ] Reduced-motion preferences are respected when applicable.

### Experience

- [ ] Loading states are intentional.
- [ ] Empty states are intentional.
- [ ] Error states provide recovery.
- [ ] Success feedback is shown when needed.
- [ ] Destructive actions communicate consequences.
- [ ] Responsive behavior was considered.
- [ ] Touch interactions do not depend on hover.

### Validation

- [ ] Relevant tests were added or updated.
- [ ] Lint was executed when available.
- [ ] Type checking was executed when available.
- [ ] Frontend tests were executed when available.
- [ ] Build was executed when applicable.
- [ ] The final diff was reviewed.

## 27. Final report

Report:

### UX improvements

Describe the user-facing improvements.

### Accessibility

Describe the accessibility checks and changes performed.

### Forms and validation

Describe masks, normalization, validation, and error behavior.

### Validation performed

List only commands and checks that were actually executed.

### Remaining limitations

State what was not tested, could not be verified, or remains dependent on
manual validation.