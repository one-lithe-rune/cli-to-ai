# cli-to-ai

Provides a `bash` script named `ai` to send prompts, with previous prompt/response history, to an LLM/ai server from the command line or a script, writing the response to STDOUT, and updating the history.

This is in `bash` rather than the more LLM/ai standard `python3` as having to muck about with yet another `.venv` for a specific tool is a pain, when I'm very likely to be in a different `.venv` tailored to what I'm actually working on.

Currently this is in 'Works for me, but probably not anyone else' state.

## Motivation

I wanted to be able to get responses and maintain conversations with an llm server from the command line in my console window, rather than launching a standalone interactive application where I wouldn't be able to pipe in prompts and redirect responses with the usual shell commands.

## Requirements

You'll need `curl` and `jq` available somewhere on your path. And `bash` obviously.

## Default Files Created

- `$HOME/.ai-connection`: contains connection and configuration setting that are sourced into the script. Edit this to change your endpoint uri, etc.
- `$HOME/.ai-default-session.json`: contain the conversation history.
  - Change the file being used with `ai --session-file FILENAME`
  - Clear the history in the current file with `ai --clear` (excluding any system prompts) or `ai --clear-all` (including system prompts).

## Some Basic Usage

Assuming the script is on your PATH and is executable:

```bash session
# set the prompt format in the connection file, currently `alpaca` and `llama3chat`
# are recognised. Any other value effectively sets `openai` chat format.

$ ai --prompt-format "alpaca"

# all the following will also append the prompt and any response to the session file

$ ai "this will send a request with this text to the llm and print the response to STDOUT"
$ ai --system "the same, but as a system prompt"
$ ai --assistant "the same, but impersonating the llm"
$ ai quoting is safer but sometimes you can get away without doing it

# use your usual shell syntax to pipe and redirect prompts and responses

$ cat prompt.txt | ai > reponse.txt

```
## TODO

- Check that `curl` and `jq` are available, and complain if not.
- Add --help/additional documentation.
- Make work with actual real OpenAI (I've been developing against local [koboldcpp](https://github.com/LostRuins/koboldcpp)/[koboldcpp-rocm](https://github.com/YellowRoseCx/koboldcpp-rocm/) which doesn't use a `model` field or care about api keys).
- Add Command-R format support.
- Improved initialisation/setup (don't just dump some defaults with my usual use case to the connection dotfile).
- Streaming? (Not sure this is possible with `curl` in a `bash` script)
- Suck less.
