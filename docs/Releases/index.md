Save file as 'monohelper.lua' in the 'autorun' directory where CE is installed

## In progress

* delete forms and unselect image on reload (mostly nicety for making changes during development of this)
* fixed parameter detection of 'single' and 'double' for XMM registers
* added filtering for field type in search window (Normal/Static/Const)
* uncheck 'HideSelection' on forms so you can see the selected items  in listViews after they've lost focus

* Evil Bank Manager
  * CountryRelationship has `PoliticSkill[] politicSkills` property, `System.Single <buyPrice>k__BackingField`, and `Country <country>k__BackingField`
  * Accessible from CountryInfoCard, CountryRelationshipCard, RegionPoliticSkillWindow, DeleteLicenseWindow, DeleteLicenseNotificationWindow, ConversationWindow
    * CountryInfoCard has various click methods (Invest, Property, Relationshop) and updates relationship value
    * CountryRelationshipCard has OnSkillClick(), OpenConversationWindow()
    * RegionPoliticSkillWindow has DeactivateTab, Apply, OnWindowShowing, OnWindowHidint, SetActiveTab
    * DeleteLicenseWindow has ONWindowShowing() and ShowNotification()
    * DeleteLicenseNotificationWindow has Accept() and Decline() as well as OnWindowShowing()
    * ConversationWindow has 

* TODO: Sample of search being buggy:
  * EvilBankManager - looking for CountryRelationshipCard, start typing and after the last 'C' in  `CountryRelationshipC` it only shows CountryRelationship.  With a 'T' instead it works for others, weird...  `RelationshipC` works fine
  * Searching for ConversationWindow buggy, 'Window' finds it
    * Ok, weird, seems to depend on pasting or not, or fixes itself
  * Might need to 'prepare' some helper functions as it seems to be spotty, next time run a single function in the LUA console to output the exact terms, etc being used for the search and allow checking of what is called in the search...
  * Pretty simple, it does `lower == nil or class.lowerName:find(lower, 1, true) ~= nil`
  * Check: return mono.formSearch.found.classes[1].lowerName
  * Look for match in `for i,class in ipairs(mono.formSearch.image.classes) do print(class.lowerName) end`
  * 
* TODO: popup on method -> generate 'find pointer' script
    * Actually adds a new memrec to CE with a script doing my standard stuff to find the pointer
    * Method 'PlayerController:Update' will globalalloc 'pPlayerController_Update'
        * push rax
        * mov rax,pPlayerController_Update
        * inc dword ptr [rax] // counter
        * mov [rax+8], rcx     // parameter 1
        * mov [rax+10], rdx    // parameter 2
        * movss [rax+18], xmm3 // parameter 3
        * pop rax
    * Table entries under script as a group header for counter and parameters
* TODO: When in 'Show usages' mode of class window, double-clicking on field should open that class
    * Currently it does the static field logic, showing 0 as the address in the console
* TODO: Figure out way to hook the right overloaded method
    * Samples in EvilBankManager:
      * UserBank:GetCapital has two versions, one takes a Single parameter, the other has none
      * UserBank:GetResourceCount takes either int or Resource/ResourceType
      * Sample: `System.Single GetResourceCount(int resourceId)`
        * monoAA_GetOverloadedFunction(SYMBOL_NAME, "UserBank:GetResourceCount", System.Single, int)
    * Could be pretty short AA script looking for parameter signatures, or lua script
* TODO: DblClick on static field generates script with {$lua} code to find it and define?
    * Alternate: LUA code to get base address for static class and define? - doesn't work with mono generate struct due to conflicting offsets, maybe CLASS_FIELD as define?
* TODO: Alternate for having memrecs for script and pointers - more difficult but cooler
    * Able to hook methods from window, list in separate window and enable/disable/remove
    * LUA could keep track of a 'globals' memory region and where the pointers for each method are
    * LUA could show count, pointers, etc in it's own window
    * LUA could open structure dissect and generate structure using it's information for names
* TODO: generate script inside mono method, not at start - three options:
    1. Simple inject - use bytes being replaced and exact address
    2. AOB - use AOB search to find point in code, bytes must remain the same
    3. Advanced AOB - allow hooking code with offset of field
        * One option would be to identify the field and change the value based on mono dissect
            * Example: 'movss [rax+5c],xmm0 // set current health'
                * 5c is offset of 'currentHealth' field, look for that offset in the type the method belongs on
                * LUA code in top of script will alter the AOB to search for
                * LUA code in top of script will search for AOB only inside method boundaries (or start of method + x + 100) for instance where x is offset we're hooking, or will stop when it finds another 'push rbp; mov rbp, rsp', or when it finds a ret (though there could be more than one of these)
                * use readmem/writemem to replace with exact code, or use AOB found in step 1 that we're replacing (how?  separate enable/disable sections)
    

## [Current Release 1.1.0](monohelper-1.1.0.lua)

Here is is as a table script in a cheat table: [MonoHelper.CT](Releases/MonoHelper.CT)

* Reconfigured build system (major change)
* Re-captioned windows 'Class' and 'Search' to 'Mono Class' and 'Mono Search'
* Fixed search window appearing off screen by centering it on screen when displayed
* Automatically select 'Assembly-CSharp' image if present (can always change from search form menu)
* First release created with new build system

## [Release 1.0.0](monohelper-1.1.0.lua)

* Original release should work fine, for posterity