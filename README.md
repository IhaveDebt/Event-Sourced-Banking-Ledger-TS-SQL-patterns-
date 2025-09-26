/**
 * Event-Sourced Banking Ledger (event_ledger.ts)
 *
 * - Minimal in-memory event store and projection for account balances.
 * - Replace with persistent SQL event table for production.
 *
 * Usage:
 *   ts-node src/event_ledger.ts demo
 */
type Event = { id: string; type: string; account: string; amount?: number; ts: number };

const store: Event[] = [];

function emit(e: Event) {
  store.push(e);
  console.log('Emitted', e);
}

function applyProjection() {
  const balances: Record<string, number> = {};
  for (const e of store) {
    balances[e.account] = balances[e.account] || 0;
    if (e.type === 'deposit') balances[e.account] += (e.amount || 0);
    if (e.type === 'withdraw') balances[e.account] -= (e.amount || 0);
  }
  return balances;
}

function demo() {
  emit({ id: 'e1', type: 'deposit', account: 'A', amount: 100, ts: Date.now() });
  emit({ id: 'e2', type: 'withdraw', account: 'A', amount: 30, ts: Date.now() });
  emit({ id: 'e3', type: 'deposit', account: 'B', amount: 200, ts: Date.now() });
  console.log('Balances:', applyProjection());
}

if (require.main === module) demo();
