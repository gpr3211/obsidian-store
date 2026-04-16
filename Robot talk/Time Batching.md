```go
package main

import (
	"sort"
	"time"
)

// TimeBatch represents a batch of tick data within a time window
type TimeBatch struct {
	StartTime time.Time
	EndTime   time.Time
	Ticks     []SymTick
}

// BatchTicks groups tick data into 5-minute intervals
func BatchTicks(ticks []SymTick, intervalMinutes int) []TimeBatch {
	if len(ticks) == 0 {
		return nil
	}

	// Sort ticks by time if they aren't already sorted
	sort.Slice(ticks, func(i, j int) bool {
		return ticks[i].Time < ticks[j].Time
	})

	// Initialize batches
	var batches []TimeBatch
	
	// Convert first timestamp to time.Time and round down to nearest interval
	firstTime := time.Unix(ticks[0].Time, 0)
	currentBatchStart := firstTime.Truncate(time.Duration(intervalMinutes) * time.Minute)
	currentBatch := TimeBatch{
		StartTime: currentBatchStart,
		EndTime:   currentBatchStart.Add(time.Duration(intervalMinutes) * time.Minute),
		Ticks:     make([]SymTick, 0),
	}

	// Group ticks into batches
	for _, tick := range ticks {
		tickTime := time.Unix(tick.Time, 0)
		
		// If tick belongs to next batch, save current batch and create new one
		if tickTime.After(currentBatch.EndTime) {
			if len(currentBatch.Ticks) > 0 {
				batches = append(batches, currentBatch)
			}
			
			// Find next batch start time
			currentBatchStart = tickTime.Truncate(time.Duration(intervalMinutes) * time.Minute)
			currentBatch = TimeBatch{
				StartTime: currentBatchStart,
				EndTime:   currentBatchStart.Add(time.Duration(intervalMinutes) * time.Minute),
				Ticks:     make([]SymTick, 0),
			}
		}
		
		currentBatch.Ticks = append(currentBatch.Ticks, tick)
	}

	// Add last batch if it contains ticks
	if len(currentBatch.Ticks) > 0 {
		batches = append(batches, currentBatch)
	}

	return batches
}

// GetBatchStatistics calculates basic statistics for a batch
func GetBatchStatistics(batch TimeBatch) BatchStats {
	if len(batch.Ticks) == 0 {
		return BatchStats{}
	}

	stats := BatchStats{
		Symbol:    batch.Ticks[0].Symbol,
		StartTime: batch.StartTime,
		EndTime:   batch.EndTime,
		Open:      batch.Ticks[0].Price,
		Close:     batch.Ticks[len(batch.Ticks)-1].Price,
		High:      batch.Ticks[0].Price,
		Low:       batch.Ticks[0].Price,
		Volume:    0,
	}

	for _, tick := range batch.Ticks {
		if tick.Price > stats.High {
			stats.High = tick.Price
		}
		if tick.Price < stats.Low {
			stats.Low = tick.Price
		}
		stats.Volume += tick.Volume
	}

	return stats
}

// BatchStats represents statistical information for a time batch
type BatchStats struct {
	Symbol    string
	StartTime time.Time
	EndTime   time.Time
	Open      float64
	High      float64
	Low       float64
	Close     float64
	Volume    float64
}```

```go
// Example usage:
ticks := []SymTick{...}  // Your tick data
batches := BatchTicks(ticks, 5)  // Group into 5-minute batches

// Process each batch
for _, batch := range batches {
    stats := GetBatchStatistics(batch)
    fmt.Printf("Batch from %v to %v: Open=%.2f, Close=%.2f, Volume=%.2f\n",
        stats.StartTime, stats.EndTime, stats.Open, stats.Close, stats.Volume)
}
```
Key features of these functions:

1. `BatchTicks`: Groups ticks into 5-minute intervals (or any interval you specify)
2. `GetBatchStatistics`: Calculates OHLCV (Open, High, Low, Close, Volume) for each batch
3. Handles sorting and time alignment automatically
4. Uses Go's built-in time package for reliable time calculations

Would you like me to:

1. Add additional statistics calculations?
2. Add functions for different time intervals (e.g., hourly, daily)?
3. Include data validation or error handling?