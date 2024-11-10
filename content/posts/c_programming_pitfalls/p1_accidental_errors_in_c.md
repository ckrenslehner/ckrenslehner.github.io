+++
title = """C errors: Missing pointer dereference"""
description = """It is to easy to make mistakes in C. Rust can help you avoid them by using compiler driven development."""

date = 2024-11-04
draft = true

[taxonomies]
tags = ["programming", "C", "rust", "error"]

[extra]
tldr = "It is to easy to make mistakes in C. Rust can help you avoid them by using compiler driven development."
+++

# A minimal C example

We have a function to get the next message. Inside the function some logic determines if there is a message available or not.
If there is a message available, the function sets the message pointer to the message string. Otherwise, it sets the message pointer to NULL.

``` C
bool get_next_message(char const ** message) {
  if (message == NULL) {
    return false;
  }

  // TODO: There is some logic which tells if there is a message available
  bool message_available = true; 

  if (message_available) {
    *message = "Hello, World!";
  }
  else {
    message = NULL;
  }

  return true;
}
```

The main function calls the `get_next_message` function in a loop until there are no more messages available.

``` C
int main(void) {
  char const * message = NULL;

  do
  {
    bool success = get_next_message(&message);

    if (success) {
      if (message != NULL) {
        printf("Message: %s\n", message);
      }
      else {
        printf("No message available\n");
      }
    }
    else {
      printf("Error while getting message\n");
    }
  } while(message != NULL);

  return 0;
}
```

So lets say we have one message available. The output of the program should be:

``` output
Message: Hello, World!
No message available 
```
---

However, the loop will never end. The reason is that the `get_next_message` function sets the message pointer to NULL when there is no message available. But it should set the message pointer to NULL by dereferencing it. So the correct implementation should be:

``` C
bool get_next_message(char const ** message) {
  if (message == NULL) {
    return false;
  }

  // TODO: There is some logic which tells if there is a message available
  bool message_available = true; 

  if (message_available) {
    *message = "Hello, World!";
  }
  else {
    // Note! Dereference the message pointer
    *message = NULL;
  }

  return true;
}
```

# The same thing in Rust
TODO: Show that this is not possible


# Nextup! Missing * and therefore comparing an address instead of a boolean

``` C
void sota_cli_standardCommandHandling(proto_ssm_rpc_RpcRequest_PointOfExecution point_of_execution, bool *reboot_needed)
{
  // handle sota cli commands with execution point "immediate" -> we are not attached to the network yet so we just
  // keep the response in the sota cli control block
  bool all_commands_handled = false;
  *reboot_needed = false;

  // Happened during a refactor
  // Attention wrong code: while(!all_commands_handled && !reboot_needed)
  while(!all_commands_handled && !*reboot_needed)
  {
    bool success = sota_cli_handleCommand(point_of_execution, &all_commands_handled, reboot_needed);
    if(!success)
    {
      my_print("Command failed");
    }
  }

  // Save backup of the sota cli state
  if(!sota_cli_saveStateToFileSystem())
  {
    my_print("Failed to save sota cli state to file system");
  }
}
```