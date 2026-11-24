
Project requirements will be recorded here.

# Beam Designer

## Platform
- Python 3.10+ desktop app using Tkinter, started with `python app.py`
- Calculations with numpy, kept in their own module, separate from
  the window code
- Diagrams drawn with matplotlib, embedded in the window

## Purpose
A beam design tool for engineers and students. The user sets up a beam,
places loads on it, and picks a steel or lumber section. The app shows the
shear, moment, and deflection diagrams and checks whether the beam is
strong and stiff enough, updating instantly as anything changes.

## Users
A civil engineering student checking a homework beam, or an engineer
sizing a floor beam or deck joist.

## Inputs
- Support type (no units): Simply supported or Cantilever (fixed at the left
  end), chosen with a selector. The default is simply supported.
- Span: 4 to 40 ft inclusive, default 20 ft, set with a slider and a number
  box that stay in sync.
- Loads, listed in a panel where each can be edited or removed:
  - Point loads: signed value (kips, positive downward and negative upward)
    and position (ft), both entered in number fields. Values may be any finite
    signed number. Position is from 0 to the span, inclusive. A new point load
    defaults to 1 kip downward at midspan (at the free end of a cantilever).
  - "Add point load" and "Add uniform load" buttons.
  - Point loads can also be dragged along the beam in the drawing,
    snapping to 0.25 ft.
  - Uniform loads: signed intensity (kips/ft, positive downward and negative
    upward), plus start and end positions (ft), entered in number fields.
    Intensity may be any finite signed number. Start must be less than end,
    and the range must overlap the beam. A range that partly extends beyond
    the beam is clipped to the beam; a range wholly outside the beam is
    invalid. A new uniform load defaults to 1 kip/ft downward over the full
    span.
- Material and section (categorical choices, no units), chosen from selectors
  using these tables. The default is steel W12x26:

  Steel W-shapes (E = 29,000 ksi, allowable bending stress Fb = 30 ksi):

  | Section | Ix (in^4) | Sx (in^3) | Weight (lb/ft) |
  |---------|-----------|-----------|----------------|
  | W8x10   | 30.8      | 7.81      | 10 |
  | W10x12  | 53.8      | 10.9      | 12 |
  | W12x26  | 204       | 33.4      | 26 |
  | W14x22  | 199       | 29.0      | 22 |
  | W16x26  | 301       | 38.4      | 26 |
  | W18x35  | 510       | 57.6      | 35 |
  | W21x44  | 843       | 81.6      | 44 |
  | W24x55  | 1350      | 114       | 55 |

  Douglas Fir-Larch No. 2 lumber (E = 1,600 ksi, Fb = 0.900 ksi):

  | Section | Ix (in^4) | Sx (in^3) |
  |---------|-----------|-----------|
  | 2x8     | 47.63     | 13.14     |
  | 2x10    | 98.93     | 21.39     |
  | 2x12    | 177.98    | 31.64     |

- Deflection limit (dimensionless ratio, with L in inches): L/240 or L/360,
  chosen with a selector (default L/360).
- Self-weight is ignored.

### Input validation and errors
- All user-entered numeric values must be present and finite. Span values
  must be between 4 and 40 ft inclusive. Point-load positions must be between
  0 and the span, inclusive. Load magnitudes have no additional cap.
- Zero load values are allowed and have no effect.
- Keep invalid typed values visible and show an inline error; do not silently
  clamp them. While any required input is invalid, mark analysis outputs,
  checks, and section recommendations unavailable rather than showing results
  from earlier inputs. Recompute immediately when all inputs are valid.

## Calculations
- Reactions from statics.
- Shear V(x) and moment M(x) along the beam, at 1 ft spacing or finer,
  plus exactly at every load point and load start/end.
- Deflection y(x) by integrating M/EI twice with the boundary conditions
  for the support type (y = 0 at both supports; or y = 0 and slope = 0 at
  the fixed end).
- Bending stress fb = |M max| x 12 / Sx (ksi). Bending check passes if
  fb <= Fb.
- Deflection check passes if |y max| <= L/240 or L/360 (L in inches).
- Lightest passing section: of the sections in the chosen material, the
  lightest steel section that passes both checks (if two weigh the same,
  the one with the larger Ix), or the first lumber size in the table
  that passes.
- Units: kips, ft, and kip-ft on screen; inches for deflection.

## Outputs
- A beam drawing with the supports, load arrows (point loads as single
  arrows, uniform loads as a row of small arrows), and the reactions.
- Three diagrams stacked under the beam and lined up with it on the same
  x scale: shear (kips), moment (kip-ft), and deflected shape (in). Each
  diagram is filled and shows its maximum value labeled at its location.
- Moving the mouse over any diagram shows a vertical line across all three
  with the values of V, M, and y at that x.
- Two check cards: Bending and Deflection. Each shows the actual value, the
  allowable value, a bar for the ratio (green under 70%, amber 70-100%,
  red over 100%), and PASS or FAIL.
- A line suggesting the lightest passing section, with a button to use it.
- Recompute and refresh outputs immediately whenever inputs are valid.
- Add a before/after comparison to show how diagrams checks change when applying a recommended section
- An Export CSV button saves x, shear V, moment M, and deflection y at each
  analyzed position, with units in the column headers, for use in Excel. The
  export uses the right-side shear value at load discontinuities.
- A Calculation Sheet PDF button saves a printable report with the current
  inputs at the top, the beam drawing and diagrams, reactions, and the selected
  section's bending and deflection checks.

### Unavailable states
- If any required input is invalid, show its inline error and mark the beam
  drawing, diagrams, check cards, and section recommendation unavailable.
  Never show results from earlier valid inputs.
- If no listed section passes both checks, show "No listed section passes
  both checks" instead of a recommendation and omit the button to use a
  suggested section. The check cards still show the results for the selected
  section.

## Look and feel
- Opens with an example already loaded (20 ft simply supported W12x26
  with a 1 kip/ft uniform load over the full span), so it is useful the
  moment it opens.
- Clean and professional, like a commercial engineering web app: light
  background, white cards with soft shadows, one accent color, a modern
  system font, generous spacing. No clutter.
- Make the visual style more playful and energetic while keeping engineering
  information clear and professional. Use BYU's blue and white school colors,
  and include the BYU logo and school slogan in a tasteful branded area.
- Inputs in a panel on the left; drawing, diagrams, and checks on the
  right. For a desktop window: inputs in a panel on the left, the beam and diagrams on the right, and a window that can be resized.
- Every input has a short label with units. Hovering over a check card
  explains the formula used.
-Display campus themed examples. Give example beams playful names or settings inspired by campus structures, while keeping all load values clearly labeled as examples.

## Validation
1. Simply supported, 20 ft, W12x26, uniform 1 kip/ft over the full span:
   reactions 10 k and 10 k; max shear 10 k; max moment 50.0 kip-ft at
   10 ft; fb = 17.96 ksi; max deflection 0.609 in at 10 ft; L/360 =
   0.667 in; both checks pass.
2. Simply supported, 20 ft, W12x26, point load 10 k at 5 ft: reactions
   7.5 k (left) and 2.5 k (right); max moment 37.5 kip-ft at 5 ft;
   fb = 13.47 ksi; max deflection 0.340 in at 8.82 ft.
3. Cantilever, 10 ft, W12x26, point load 5 k at the free end: reaction
   5 k and fixed-end moment 50 kip-ft; max deflection 0.487 in at the free
   end (PL^3/3EI); L/360 = 0.333 in, so deflection fails.
4. Simply supported 12 ft 2x10 joist, uniform 0.05 kip/ft: max moment
   0.90 kip-ft; fb = 0.505 ksi; max deflection 0.147 in; both pass.
5. Input boundaries: spans of 4 and 40 ft are valid; spans outside this range
   are invalid. Point loads at 0 and at the span are valid, but positions
   outside that interval are invalid. A uniform load from -1 to 2 ft on a
   10 ft beam is clipped to 0 to 2 ft; a range wholly outside the beam or
   with start at or beyond end is invalid. Blank or non-finite numeric
   entries are invalid. Invalid entries show inline errors and do not display
   analysis results or pass/fail checks.
   
   Confirm by this validation:  Simply supported, 30 ft, W18x35, uniform 2 kips/ft from 0 to 15 ft
   only: reactions 22.5 k (left) and 7.5 k (right); max moment
   126.6 kip-ft at 11.25 ft.

## Out of scope
- Self-weight, shear stress, lateral-torsional buckling, load
  combinations, and continuous beams.

## Save and open
- A File menu provides Save and Open commands.
- Designs are versioned JSON files that preserve support type, span, material,
  section, deflection limit, and all point and uniform loads.
- Save validates the current inputs. The first save prompts for a file path;
  subsequent saves write to that path.
- Open validates a design before replacing the current inputs. Invalid files
  show an error and leave the current design unchanged.

## Build Stages

Build the Python desktop app in testable increments. Keep the Tkinter window
in `app.py` and all beam calculations in `beam_calculations.py`; calculation
tests must run without creating a Tk window.

1. **Desktop window and layout**
   - Create the resizable Tkinter window, BYU-branded header, left input panel,
     right analysis panel, diagram placeholders, check cards, and recommendation
     area. Load the default example.
   - **Check:** Run `python app.py`; confirm the window opens and resizes, with
     all required areas visible and legible.

2. **Inputs and validation**
   - Add support, span, load, material/section, and deflection-limit controls.
     Add, edit, and remove loads; implement 0.25 ft point-load dragging; show
     field-level validation and unavailable result states.
   - **Check:** Exercise defaults, load editing, span boundaries, point-load
     endpoints, uniform-load clipping/rejection, blank/non-finite values, and
     dragging. Invalid inputs must not leave stale results visible.

3. **Reactions, shear, and moment**
   - Implement statics for both support types and signed point and uniform
     loads in the UI-independent calculation module. Plot the diagrams on the
     beam's shared x scale.
   - **Check:** Run `python -m unittest discover -s tests -v`; verify reactions
     and diagrams for validation cases 1-3 and the partial-uniform-load case.

4. **Deflection and selected-section checks**
   - Integrate the moment response with the support boundary conditions.
     Calculate bending stress and deflection checks for the selected section.
   - **Check:** Verify cases 1-4, including values, units, pass/fail, and
     unavailable outputs for invalid inputs.

5. **Recommendations and comparison**
   - Evaluate sections in the selected material, use the specified weight
     ordering and steel tie-break, and handle the no-passing-section state.
     Applying a suggestion should show before/after checks and deflected shape.
   - **Check:** Test passing and failing recommendations, both materials, the
     steel tie-break, and applying a suggested section.

6. **Interaction and acceptance polish**
   - Add linked diagram cursor values, keyboard inspection, tooltips, campus
     examples, final BYU styling, save/open, CSV and calculation-sheet PDF
     export, and responsive/error-state refinements.
   - **Check:** Test arbitrary diagram inspection, keyboard-operable controls,
     window resizing, unavailable states, and all prior validation cases.
     Save and reopen a design with both load types; reject malformed and
     invalid design files without changing the open design.
     Export CSV and verify the header, units, row values, and Excel-compatible
     encoding.
     Export a calculation-sheet PDF and verify its inputs, diagrams, reactions,
     and selected-section checks.