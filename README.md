# react-js-testing

### Hooks testing

##### Test useState, useEffect, custom hooks (useAverage) example:

- Component `useAverage` (useAverage.ts)

```js
import { useEffect, useState } from 'react';

function useAverage(grades) {
  const [average, setAverage] = useState(0);
  const [totalSum, setTotalSum] = useState(0);
  const [numGrades, setNumGrades] = useState(grades.length);

  useEffect(() => {
    const sum = grades.reduce((acc, grade) => acc + grade, 0);
    setTotalSum(sum);
    setNumGrades(grades.length);
    if (grades.length > 0) {
      setAverage(sum / grades.length);
    } else {
      setAverage(0); // If no grades, average is 0
    }
  }, [grades]);

  return { average, totalSum, numGrades };
}

export default useAverage;
```

- `useAverage.test.js`

```js
import { renderHook, act } from '@testing-library/react-hooks';
import { describe, it, expect } from 'vitest';
import useAverage from './useAverage';

vi.mock('hooks/useAverage');
let mockedAverage: MockedFunction<any>;

describe('useAverage', () => {
    beforeEach(() => {
      vi.clearAllMocks();
      mockedAverage = vi.mocked(useAverage);
    });


  it('should calculate initial values correctly', () => {
    mockedAverage.mockReturnValue([10, 9, 8, 7, 6]);

    const { result } = renderHook(() => useAverage());

    expect(result.current.average).toBe(8); // Average of [10, 9, 8, 7, 6] = 40 / 5
    expect(result.current.totalSum).toBe(40); // Sum of the grades
    expect(result.current.numGrades).toBe(5); // Number of grades
  });

  it('should update values when grades change', () => {
    mockedAverage.mockReturnValue([10, 9, 8]);
    
    const { result, rerender } = renderHook(() => useAverage());

    act(() => {
      mockedAverage.mockReturnValue([10, 9, 8]);
      rerender(); // Trigger rerender to recalculate values
    });

    expect(result.current.average).toBe(8.5); // Average of [10, 9, 8, 7] = 34 / 4
    expect(result.current.totalSum).toBe(34); // New sum of grades
    expect(result.current.numGrades).toBe(4); // Updated number of grades
  });

  it('should handle empty array', () => {
    mockedAverage.mockReturnValue([]);
    
    const { result, rerender } = renderHook(() => useAverage());

    expect(result.current.average).toBe(0); // Average of empty array is 0
    expect(result.current.totalSum).toBe(0); // Sum of empty array is 0
    expect(result.current.numGrades).toBe(0); // No grades
  });
});
```
