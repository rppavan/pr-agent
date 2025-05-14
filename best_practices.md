
<b>Pattern 1: Always wrap API calls and potentially error-prone operations with try-except blocks to ensure proper error handling and prevent application crashes.</b>

Example code before:
```
response = client.messages.count_tokens(
    model="claude-3-7-sonnet-20250219",
    system="system",
    messages=[{
        "role": "user",
        "content": patch
    }],
)
return response.input_tokens
```

Example code after:
```
try:
    response = client.messages.count_tokens(
        model="claude-3-7-sonnet-20250219",
        system="system",
        messages=[{
            "role": "user",
            "content": patch
        }],
    )
    return response.input_tokens
except Exception as api_error:
    get_logger().error(f"Error in API call: {api_error}")
    return fallback_value
```

<details><summary>Examples for relevant past discussions:</summary>

- https://github.com/qodo-ai/pr-agent/pull/1644#discussion_r2068039198
- https://github.com/qodo-ai/pr-agent/pull/1644#discussion_r2013912636
- https://github.com/qodo-ai/pr-agent/pull/1263#discussion_r1782129216
</details>


___

<b>Pattern 2: Replace print statements with proper logging using get_logger() to ensure consistent error tracking and monitoring across the application.</b>

Example code before:
```
print(f"Failed to fetch sub-issues. Error: {e}")
```

Example code after:
```
get_logger().error(f"Failed to fetch sub-issues", artifact={"error": e})
```

<details><summary>Examples for relevant past discussions:</summary>

- https://github.com/qodo-ai/pr-agent/pull/1529#discussion_r1958684550
- https://github.com/qodo-ai/pr-agent/pull/1529#discussion_r1958686068
- https://github.com/qodo-ai/pr-agent/pull/1529#discussion_r1964107962
- https://github.com/qodo-ai/pr-agent/pull/1634#discussion_r2007976915
</details>


___

<b>Pattern 3: Validate input parameters before processing to prevent runtime errors, especially when working with optional or user-provided data.</b>

Example code before:
```
if not (comment_id or (file_path and line_number)):
    return
```

Example code after:
```
# Return if any required parameter is missing
if not all([comment_id, file_path, line_number]):
    get_logger().info("Missing required parameters for conversation history")
    return
```

<details><summary>Examples for relevant past discussions:</summary>

- https://github.com/qodo-ai/pr-agent/pull/1687#discussion_r2036939244
- https://github.com/qodo-ai/pr-agent/pull/1290#discussion_r1798939921
- https://github.com/qodo-ai/pr-agent/pull/1529#discussion_r1958698147
</details>


___

<b>Pattern 4: Use defensive programming when handling external API responses or data structures to account for unexpected formats or missing fields.</b>

Example code before:
```
model = get_settings().config.model.lower()
if force_accurate and 'claude' in model and get_settings(use_context=False).get('anthropic.key'):
    return self.calc_claude_tokens(patch)
```

Example code after:
```
# Ensure model is a string and not None before operations
if model is None:
    get_logger().warning("Model is None, cannot determine model type accurately")
    return encoder_estimate

if not isinstance(model, str):
    get_logger().warning(f"Model is not a string type: {type(model)}")
    return encoder_estimate
    
if force_accurate and 'claude' in model.lower() and get_settings(use_context=False).get('anthropic.key'):
    return self.calc_claude_tokens(patch)
```

<details><summary>Examples for relevant past discussions:</summary>

- https://github.com/qodo-ai/pr-agent/pull/1644#discussion_r2032621065
- https://github.com/qodo-ai/pr-agent/pull/1391#discussion_r1879875489
- https://github.com/qodo-ai/pr-agent/pull/1391#discussion_r1879875496
</details>


___

<b>Pattern 5: Optimize code to reduce unnecessary API calls and improve performance, especially when working with external services or databases.</b>

Example code before:
```
# Get the original comment to find its location
comment = self.pr.get_comment(comment_id)
if not comment:
    return []
    
# Get all comments
all_comments = list(self.pr.get_comments())
```

Example code after:
```
# Fetch all comments with a single API call
all_comments = list(self.pr.get_comments())

# Find the target comment by ID
target_comment = next((c for c in all_comments if c.id == comment_id), None)
if not target_comment:
    return []
```

<details><summary>Examples for relevant past discussions:</summary>

- https://github.com/qodo-ai/pr-agent/pull/1687#discussion_r2051644060
- https://github.com/qodo-ai/pr-agent/pull/1555#discussion_r1963228113
- https://github.com/qodo-ai/pr-agent/pull/1687#discussion_r2051640437
</details>


___

<b>Pattern 6: Ensure proper error logging with context by using structured logging with artifacts parameter rather than string concatenation.</b>

Example code before:
```
get_logger().error(f"Failed to get log context: {e}")
```

Example code after:
```
get_logger().error("Failed to get log context", artifact={"error": e})
```

<details><summary>Examples for relevant past discussions:</summary>

- https://github.com/qodo-ai/pr-agent/pull/1634#discussion_r2007976915
- https://github.com/qodo-ai/pr-agent/pull/1529#discussion_r1964110734
- https://github.com/qodo-ai/pr-agent/pull/1529#discussion_r2055606721
</details>


___

<b>Pattern 7: Add clear, descriptive comments for complex logic or non-obvious code to improve maintainability and help future developers understand the code's purpose.</b>

Example code before:
```
if not isinstance(issue, dict):
    continue
```

Example code after:
```
# Skip empty issues or non-dictionary items to ensure valid data structure
if not isinstance(issue, dict):
    continue
```

<details><summary>Examples for relevant past discussions:</summary>

- https://github.com/qodo-ai/pr-agent/pull/1262#discussion_r1782097204
- https://github.com/qodo-ai/pr-agent/pull/1583#discussion_r1971790979
- https://github.com/qodo-ai/pr-agent/pull/1687#discussion_r2051641090
</details>


___
