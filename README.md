# cli-to-ai

Provides a `bash` script named `ai` to send prompts, with previous prompt/response history, to an LLM/ai server from the command line or a script, writing the response to STDOUT, and updating the history.

This is in `bash` rather than the more LLM/ai standard `python3` as having to muck about with yet another `.venv` for a specific tool is a pain, when I'm very likely to be in a different `.venv` tailored to what I'm actually working on.

## Motivation

I wanted to be able to get responses and maintain conversations with an llm server from the command line in my console window, rather than launching a standalone interactive application where I wouldn't be able to pipe in prompts and redirect responses with the usual shell commands.

## Alternatives

You might want to look at these as possible alternatives:

* [mods](https://github.com/charmbracelet/mods) - same idea but in Go and prettier.
* [Wave](https://github.com/wavetermdev/waveterm) - full terminal implementation, with inbuilt llm querying.

## What's New

- 2024-07-09
  - Added `--cat` listing out the whole of the current session's conversation.
- 2024-07-02
  - Added `--help`
  - Added `--sampler-settings [FILENAME]`. Reads in a JSON file with sampler with the sampler settings to use. See the `sampler-settings*.json` files for examples. Since not all backends support all sampler types, connection files now support a `samplers=` line listing which json keys from a sampler file to send.
- 2024-06-25
  - Added `--load-connection` and `--save-connection` allowing connection setups to be loaded from and saved to files. As before any connection setup information loaded is then saved to `$HOME/.ai-connection` so it persists between runs of the script.
  - Now works against the real OpenAI chat API cloud endpoint at https://api.openai.com/ configure using:
    - `--endpoint [uri]` to set the endpoint to send the requests to.
    - `--model [model_name]` to set the model for requests.
    - `--api-key-var [envar_name]` to set which environment variable to read an API key from.
    - As an example, the file `connection-remote-openai-gpt4o` contains a connection configuration for the OpenAI chat API endpoint, using the GPT-4 Omni model, reading the API key from your `OPENAI_API_KEY` environment variable.
  - Added example connection setup files for [koboldcpp](https://github.com/LostRuins/koboldcpp)/[koboldcpp-rocm](https://github.com/YellowRoseCx/koboldcpp-rocm/) and [OpenAI](https://openai.com/)

## Requirements

You'll need `curl` and `jq` available somewhere on your path. And `bash` obviously.

## Default Files Created

- `$HOME/.ai-connection`: contains connection and configuration setting that are sourced into the script. Edit this to change your endpoint uri, etc.
- `$HOME/.ai-default-session.json`: contains the conversation history.
  - Change the file being used with `ai --session-file FILENAME`
  - Clear the history in the current file with `ai --clear` (excluding any system prompts) or `ai --clear-all` (including system prompts).

## Some Basic Usage

Assuming the script is on your PATH and is executable:

```bash session
# save and load your connection/config values from the specified file
$ ai --save-connection ./connection-local-my-server
$ ai --load-connection ./connection-remote-company-server

# set the prompt format in the connection file, currently `alpaca` and `llama3chat`
# are recognised. Any other value effectively sets `openai` chat format.

$ ai --prompt-format "alpaca"

# all the following will also append the prompt and any response to the session file

$ ai "this will send a request with this text to the llm and print the response to STDOUT"
$ ai --system "the same, but as a system prompt"
$ ai --assistant "the same, but impersonating the llm"
$ ai quoting is safer but sometimes you can get away without doing it

# use your usual shell syntax to pipe and redirect prompts and responses

$ cat prompt.txt | ai > response.txt
```

## Sample Connection Setup Files in this Repo

These can be loaded with `ai --load-connection` (see above):

- `connection-local-koboldcpp-llama3chat`: Connects to a local koboldcpp server running on its default port, suitable for any model using the Llama 3 chat prompt format.
- `connection-local-koboldcpp-alpaca`: Connects to a local koboldcpp server running on its default port, suitable for models using the Alpaca prompt format.
- `connection-remote-openai-gpt4o-chat`: Connects to the OpenAI chat API cloud endpoint. You will need to have set up your OpenAI account configured for API access, and have set your `OPENAI_API_KEY` environment variable to an API key value you've created on your account.

Note that since I only have support for OpenAI compatible endpoints coded so far, the `koboldcpp` connections are against that endpoint provided by your `koboldcpp` server rather than its native `KoboldAI` one.

## TODO

- Check that `curl` and `jq` are available, and complain if not.
- ~~Add --help/additional documentation.~~ `--help` is now in, but still needs improvement
- Add Command-R format support.
- ~~Ability to output full conversation history to STDOUT~~, optionally filtered by role.
- ~~Improved initialisation/setup (don't just dump some defaults with my usual use case to the connection dotfile).~~ (now use `--load-connection` with one of the connection files in this repo as a starting point, could still do with improvement)
- Native KoboldAI endpoint support.
- Llama.cpp server endpoint support.
- Streaming? (Not sure this is possible with `curl` in a `bash` script)
- Suck less.
