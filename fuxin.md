- command + d = option + click: copy ui item
- pdf vector image no need @2,@3 size image
- button text can change line ,label cannot
- check layout inferred to use constraints
- stackview spacing is the minimum space
- image dark mode: appearance->any,dark will enable modes in assets
- button size:configuration -> point size or scale
- button color: tint color or foreground
- api para has to be encoded af provides method to solve this problem
- tableview/cell:row height,
- cell button image change can be set in awakeFromNib
- button background color changes if clicked to disable color change tint to clear color
- add event:addAction/addtarget
- style:inset group
- navibar:prefer large titles will change navibar title to big theme
- barbuttonitem size:navigationItem.rightBarButtonItem?.Image=(systemName:,withConfiguration:),size can be specified by withConfiguration
- text view:a subclass of scrollview,so normally will disable scrolling/indicators.
- textview multiline fix:use textviewdelegate.textviewdidchange to inform cell update. tableview.beginUpdates,tableview.endUpdates(old way).now use tableview.performBatchUpdates
- navibar tint will be inheritted by all barbuttonitems and so on
- tableview selection style ib available
- system control language can be set by localization.
- delete button text also can be set by delegate methods.
- editbuttonitem:setEditing is called on click. image and title can only be set by one.
- separator inset(nei bian ju)
- editingStyleForRowAt
- swipe delete: implement tableview(commit:forrowat:)
- shouldindentwhileeditingrowat
- uibutton.setTitle change text without changing font size? -> Change your button Style from Plain to Default
