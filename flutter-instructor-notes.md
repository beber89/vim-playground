# In lecture
## Outline
### Dart Basics
### Flutter Basics
### Widgets Styling
### Widgets with State
### Adaptive UI
### Widgets Lifecycle
## Dart Basics
### Variables
### Functions
### Conditionals
- null-aware operators
 - the ?? conditional null check > instance1IfNotNull?? instance2IfInstance1IsNull
 - outgoing[a]?.contains(b)
### Classes, Private properties, getters
- Constructors & named arguemnts
### Lists
### Enums & Multiple Constructors
- Colors class acts like an Enum but it is not. 
 - it uses static objects as members.
### Maps
### Passing callback functions
### final vs const
## Flutter Basics
- AppBar(title: 'text') incorrect, even Text shall be a widget Text()
- Show a StatelessWidget with a changing property not responding to change
 - Here I introduce the StatefulWidget
- update stateful widget without setState() and show why screen not rerendered.
## Widgets, Styling & Logic
- Most Important/Essential Widgets 
- Do not be overwhelmed by the variety of widgets just know what you need
- let's play a bit with Card() styling
- Learn about sizing by trying with containers
- final is a runtime constant that does not change once created.
- Introduce the map function inside a column widget.
 - Since it is impractical to hardcode the children of column by the count of elements.
- Third party: intl > for date formatting.
 - indentation is important in .yaml file
- Textfield update using callbacks & using TextControllers.
- Q: How does SingleChildScrollView work ? Needs height identified ?
- From ScrollView to ListView
 - Also ListView Requires Container with defined height.
 - Ways of using ListView | children or builder 
 - using "children" offers limited optimization.
- TextField in ModalBottomSheet is blocked when keyboard pops up.
 - BUG: when Modal is touched it pops away.
 - Block this tap response using a GestureDetector around it.
 - Explain the widget tree before this part.
