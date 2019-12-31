<script>
  import { action } from "tns-core-modules/ui/dialogs";
  import { Template } from "svelte-native/components";

  let todos = [];
  let dones = [];
  let textFieldValue = "";

  const onItemTap = args => {
    action("What do you want to do with this task?", "Cancel", [
      "Mark completed",
      "Delete forever"
    ]).then(result => {
      console.log(result); // Logs the selected option for debugging.
      let item = todos[args.index];
      switch (result) {
        case "Mark completed":
          dones = [item, ...dones]; // Places the tapped active task at the top of the completed tasks.
          todos = todos.filter(t => t !== item); // Removes the tapped active  task.
          break;
        case "Delete forever":
          todos = todos.filter(t => t !== item); // Removes the tapped active task.
          break;
        case "Cancel" || undefined: // Dismisses the dialog
          break;
      }
    });
  };

  const onButtonTap = () => {
    if (textFieldValue === "") return; // Prevents users from entering an empty string.
    console.log("New task added: " + textFieldValue + "."); // Logs the newly added task in the console for debugging.
    todos = [{ name: textFieldValue }, ...todos]; // Adds tasks in the ToDo array. Newly added tasks are immediately shown on the screen.
    textFieldValue = ""; // Clears the text field so that users can start adding new tasks immediately.
  };

  const onDoneTap = args => {
    action("What do you want to do with this task?", "Cancel", [
      "Mark To Do",
      "Delete forever"
    ]).then(result => {
      console.log(result); // Logs the selected option for debugging.
      let item = dones[args.index];
      switch (result) {
        case "Mark To Do":
          todos = [item, ...todos]; // Places the tapped active task at the top of the completed tasks.
          dones = dones.filter(t => t !== item); // Removes the tapped active  task.
          break;
        case "Delete forever":
          dones = dones.filter(t => t !== item); // Removes the tapped active task.
          break;
        case "Cancel" || undefined: // Dismisses the dialog
          break;
      }
    });
  };
</script>

<style>
  button {
    font-size: 15;
    font-weight: bold;
    color: white;
    background-color: #581FE5;
    height: 40;
    margin-top: 10;
    margin-bottom: 10;
    margin-right: 10;
    margin-left: 10;
    border-radius: 20px;
  }

  textField {
    font-size: 20;
    color: #222;
    margin-top: 10;
    margin-bottom: 10;
    margin-right: 5;
    margin-left: 20;
  }

  .todo-item {
    font-size: 20;
    margin-left: 20;
    padding-top: 5;
    padding-bottom: 10;
  }

  .todo-item.active {
    font-weight: bold;
    color: #3013AC;
  }

  .todo-item.completed {
    color: #424242;
    text-decoration: line-through;
  }
</style>

<page class="page">
  <actionBar title="My Tasks" class="action-bar" />

  <tabView
    androidTabsPosition="bottom"
    selectedTabTextColor="#2847D2"
    tabTextFontSize="15">

    <tabViewItem title="To Do" textTransform="uppercase" textWrap="true">
      <gridLayout columns="2*,*" rows="*, 3*">
        <!-- Configures the text field and ensures that pressing Return on the keyboard 
		produces the same result as tapping the button. -->
        <textField
          col="0"
          row="0"
          bind:text={textFieldValue}
          hint="Type new task..."
          editable="true"
          on:returnPress={onButtonTap} />
        <button col="1" row="0" text="Add task" on:tap={onButtonTap} />

        <listView
          class="list-group"
          items={todos}
          on:itemTap={onItemTap}
          separatorColor="transparent"
          row="1"
          colSpan="2">
          <Template let:item>
            <label
              text={item.name}
              class="list-group-item-heading todo-item active"
              textWrap="true" />
          </Template>
        </listView>
      </gridLayout>
    </tabViewItem>

    <tabViewItem title="Completed" textTransform="uppercase">
      <listView
        class="list-group"
        items={dones}
        on:itemTap={onDoneTap}
        row="1"
        colSpan="2"
        separatorColor="transparent">
        <Template let:item>
          <label
            text={item.name}
            class="list-group-item-heading todo-item completed"
            textWrap="true" />
        </Template>
      </listView>
    </tabViewItem>

  </tabView>
</page>
