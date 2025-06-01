# 🤖 AI Prompt Analysis - What the AI is Being Asked

## 📋 **Current AI Request Structure**

### **System Prompt (Enhanced with Context):**
```
You are an expert Mermaid diagram syntax corrector specializing in {DIAGRAM_TYPE} diagrams.

Your task is to fix the provided Mermaid code to make it valid and renderable. You must respond with a JSON object containing:
1. fixed_code: The corrected Mermaid diagram code (string)
2. changes_made: Array of specific changes you made (array of strings)  
3. confidence: Your confidence level in the fix from 0-100 (number)

Consider these context details:
- Diagram type: {DIAGRAM_TYPE}
- Error line: {ERROR_LINE}
- Code length: {CODE_LENGTH} characters
- Common issues detected: {ISSUES_COUNT}

Focus on fixing syntax errors while preserving the original intent and structure of the diagram.
```

### **User Prompt (With Error Context):**
```
Fix this Mermaid diagram code that has syntax errors:

Error: {ERROR_MESSAGE}
Error appears around line: {ERROR_LINE}

Detected issues:
- Line {LINE}: {ISSUE} ({SUGGESTION})
- Line {LINE}: {ISSUE} ({SUGGESTION})

Code to fix:
{MERMAID_CODE}
```

## 🔧 **OpenRouter Structured Outputs Implementation**

### **JSON Schema Enforced:**
```json
{
  "type": "json_schema",
  "json_schema": {
    "name": "mermaid_fix_response",
    "strict": true,
    "schema": {
      "type": "object",
      "properties": {
        "fixed_code": {
          "type": "string",
          "description": "The corrected Mermaid diagram code that is valid and renderable"
        },
        "changes_made": {
          "type": "array",
          "description": "List of specific changes made to fix the code",
          "items": {
            "type": "string"
          }
        },
        "confidence": {
          "type": "number",
          "description": "Confidence level in the fix (0-100)",
          "minimum": 0,
          "maximum": 100
        }
      },
      "required": ["fixed_code", "changes_made", "confidence"],
      "additionalProperties": false
    }
  }
}
```

## 📊 **Expected AI Response Format:**

### **Structured Output (Primary):**
```json
{
  "fixed_code": "graph TD\n    A[Start] --> B{Decision?}\n    B -->|Yes| C[Action 1]\n    B -->|No| D[Action 2]\n    C --> E[End]\n    D --> E",
  "changes_made": [
    "Fixed arrow syntax: changed '--' to '-->' on lines 2-3",
    "Added missing closing bracket for node B",
    "Corrected decision node syntax with curly braces",
    "Added proper edge labels with pipe syntax"
  ],
  "confidence": 95
}
```

### **Fallback Response (If Structured Outputs Not Supported):**
```
graph TD
    A[Start] --> B{Decision?}
    B -->|Yes| C[Action 1]
    B -->|No| D[Action 2]
    C --> E[End]
    D --> E
```

## 🎯 **Context Variables Provided to AI:**

### **Error Context:**
- **Diagram Type**: flowchart, sequence, class, state, er, journey, gantt, pie, gitgraph
- **Error Line**: Extracted from Mermaid error messages using regex patterns
- **Error Message**: Full error text from Mermaid parser
- **Code Length**: Character count for complexity assessment
- **Common Issues**: Pre-analyzed syntax problems with suggestions

### **Example Context for Flowchart:**
```javascript
{
  diagramType: "flowchart",
  errorLine: 3,
  errorMessage: "Parse error on line 3: Expecting 'ARROW_POINT'",
  codeLength: 156,
  commonPatterns: [
    {
      line: 2,
      issue: "Incomplete arrow syntax",
      suggestion: "Use --> for arrows"
    },
    {
      line: 4,
      issue: "Spaces in labels should be quoted",
      suggestion: "Use quotes around labels with spaces"
    }
  ]
}
```

## 🔄 **Fallback Mechanism:**

### **If Structured Outputs Fail:**
1. **Detect Error**: Check if error message contains 'structured' or 'json_schema'
2. **Retry with Simple Prompt**: Use basic system/user prompt without JSON schema
3. **Parse Response**: Treat response as plain Mermaid code
4. **Apply Fix**: Use fallback parsing with default confidence

### **Fallback System Prompt:**
```
You are an expert Mermaid diagram syntax corrector. Fix the provided Mermaid code to make it valid and renderable. Return ONLY the corrected Mermaid code without explanations.
```

### **Fallback User Prompt:**
```
Fix this Mermaid code:

Error: {ERROR_MESSAGE}

Code:
{MERMAID_CODE}
```

## 📈 **Benefits of This Approach:**

### **Enhanced Context:**
- ✅ Diagram-type-specific expertise
- ✅ Line-specific error targeting
- ✅ Pre-analyzed common issues
- ✅ Confidence scoring

### **Structured Outputs:**
- ✅ Guaranteed JSON format
- ✅ Detailed change tracking
- ✅ Confidence assessment
- ✅ Type-safe parsing

### **Reliability:**
- ✅ Fallback for unsupported models
- ✅ Multiple error handling layers
- ✅ Graceful degradation
- ✅ Consistent user experience

## 🎯 **Testing the AI Prompts:**

### **To See What AI Receives:**
1. Open browser developer tools (F12)
2. Go to Network tab
3. Trigger an AI fix
4. Look for the OpenRouter API call
5. Examine the request payload

### **Console Logging:**
The application logs enhanced features and cache statistics to help you understand what's happening behind the scenes.

---

*This analysis shows exactly what prompts and context the AI receives, ensuring transparency and enabling optimization of the AI interaction.*
