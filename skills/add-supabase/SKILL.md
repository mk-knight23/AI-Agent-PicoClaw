---
name: add-supabase
description: "Adds Supabase as PicoClaw's persistent backend using the Supabase REST API via Go's net/http. No heavy ORM — plain HTTP requests with JSON marshaling. Creates db/supabase.go with typed table helpers, db/migrations/001_init.sql for schema, and registers memory tools. Compiles to a static binary with zero CGo. Requires a Supabase project URL and anon key."
---

# add-supabase

Supabase backend for PicoClaw: plain `net/http` REST calls, JSON structs, zero CGo, static binary.

## Usage
```
/add-supabase
```

## Files Created
```
db/supabase.go                  # Typed Supabase REST client using net/http
db/migrations/001_init.sql      # Schema: agent_memory, skill_invocations
tools/memory.go                 # memory_read / memory_write tools
```

## Files Modified
```
tools/registry.go               # Register memory_read, memory_write
config/config.go                # Add SupabaseURL, SupabaseAnonKey
```

## Environment Variables
```
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_ANON_KEY=your_anon_key
```

## Tables (run in Supabase SQL Editor)
```sql
CREATE TABLE agent_memory (
  key TEXT PRIMARY KEY,
  value JSONB NOT NULL,
  updated_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE skill_invocations (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  skill_name TEXT NOT NULL,
  duration_ms INTEGER,
  ts TIMESTAMPTZ DEFAULT now()
);
```

## Code Sample
```go
// db/supabase.go (generated)
package db

import (
	"bytes", "encoding/json", "fmt", "io", "net/http", "os"
)

func MemoryGet(key string) (any, error) {
	url := fmt.Sprintf("%s/rest/v1/agent_memory?key=eq.%s&select=value", os.Getenv("SUPABASE_URL"), key)
	req, _ := http.NewRequest("GET", url, nil)
	req.Header.Set("apikey", os.Getenv("SUPABASE_ANON_KEY"))
	req.Header.Set("Authorization", "Bearer "+os.Getenv("SUPABASE_ANON_KEY"))
	resp, err := http.DefaultClient.Do(req)
	if err != nil { return nil, err }
	defer resp.Body.Close()
	body, _ := io.ReadAll(resp.Body)
	var rows []struct{ Value any `json:"value"` }
	json.Unmarshal(body, &rows)
	if len(rows) == 0 { return nil, nil }
	return rows[0].Value, nil
}
```

## Why net/http instead of an SDK?
CGo would break cross-compilation. `net/http` is pure Go, compiles to any target, and the Supabase REST API is simple enough that a thin wrapper is sufficient.
