# @platform/internal-logic

Mathematical and statistical calculation engine for ranked ballot voting systems. Implements 3-2-1 weighted Borda scoring, paired covariance and variance estimators, Z-score hypothesis testing for rank separation, Empirical Bayesian shrinkage for exposure regularization, and multi-factor rank skew anomaly detection.

---

## Installation

The package is installed straight from this repository, pinned to a release tag:

```bash
npm install git+https://github.com/SeraphInteractive/Platform-Internal-Logic.git#v1.0.0
```

which records in `package.json`:

```json
{
  "dependencies": {
    "@platform/internal-logic": "git+https://github.com/SeraphInteractive/Platform-Internal-Logic.git#v1.0.0"
  }
}
```

npm clones the tag, runs the `prepare` script (`tsc`) and installs only `dist/`, so consumers
need `git` on the PATH but no build step of their own. The lockfile pins the exact commit.

### Releasing

1. Bump `version` in `package.json` and merge to `main` (CI runs typecheck, tests and a
   trial install of the package from git).
2. Tag and push: `git tag v1.1.0 && git push origin v1.1.0`.
3. In consumers, bump the `#v…` ref and run `npm install`.

---

## API Reference & Usage

```typescript
import {
  validate_ballot,
  aggregate_scores,
  evaluate_rank_separation,
  calculate_bayesian_shrinkage,
  analyze_raid_risk,
  type Ballot,
} from '@platform/internal-logic';

// 1. Ballot validation (3-2-1 allocation, uniqueness, completeness)
const ballot: Ballot = {
  voterId: 'user_123',
  rank1: 'entry_a', // 3 points
  rank2: 'entry_b', // 2 points
  rank3: 'entry_c', // 1 point
};
const validation = validate_ballot(ballot);

// 2. Score aggregation & 6N conservation verification
const { scores, isConserved, leaderboard } = aggregate_scores(
  ['entry_a', 'entry_b', 'entry_c'],
  [ballot]
);

// 3. Paired Z-score rank separation test
const separation = evaluate_rank_separation(
  scores.get('entry_a')!,
  scores.get('entry_b')!,
  [ballot]
);

// 4. Empirical Bayesian shrinkage
const shrunkScore = calculate_bayesian_shrinkage(
  scores.get('entry_a')!,
  20, // Total entries in universe
  100 // Total ballots cast
);

// 5. Rank skew anomaly analysis
const telemetry = analyze_raid_risk(scores.get('entry_a')!);
```

---

## Testing

Execute the test suite:
```bash
npm test
```
