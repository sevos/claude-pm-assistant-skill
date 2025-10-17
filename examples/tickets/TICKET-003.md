---
title: Design database schema for digest storage
type: Feature
status: Backlog
created_at: '2025-10-17T23:13:51.807592'
parent: TICKET-002
estimate: 2
---

## Context

Before implementing the digest generation and delivery system, we need a well-designed database schema to store digest preferences, history, and configuration.

- Problem it solves: Blocks API and delivery work from starting
- User impact: Enables efficient digest content storage and retrieval
- Business value: Foundation for reliable digest feature

## Requirements

**Functional requirements**:
- Store per-user digest preferences (frequency, time, enabled/disabled)
- Store generated digest content and metadata
- Track digest send attempts and delivery status
- Store user interaction with digests (opens, clicks, unsubscribes)
- Support historical queries for analytics

**Non-functional requirements**:
- Schema must support 1M+ active users
- Queries for user preferences must complete in < 10ms
- Digest content table must efficiently store large text/HTML payloads
- Support partitioning by date for digest archives

**Integration points**:
- User table (FK to users)
- Email logs table (digest delivery tracking)

## Acceptance Criteria

- [ ] Schema design document created with ER diagram
- [ ] All tables and columns documented
- [ ] Indexes defined for performance-critical queries
- [ ] Foreign key relationships defined
- [ ] Migration script written and tested
- [ ] Schema reviewed and approved by backend team
- [ ] Estimated query performance meets requirements

### Database Tables

- [ ] `user_digest_preferences` - User settings (frequency, time zone, enabled flag)
- [ ] `digests` - Generated digest content and metadata
- [ ] `digest_sends` - Delivery attempt tracking (status, timestamp, email service response)
- [ ] `digest_interactions` - User interactions (opens, clicks, unsubscribes)

## Open Questions

- [Engineering] Should we use PostgreSQL partitioning or archive to separate table?
- [Engineering] What retention period for old digest records?
- [Database] What are the expected query patterns for digest retrieval?

## Dependencies

- Blocks: API implementation (TICKET-004), Email templates (future)
- Related to: User authentication (TICKET-001) - digest prefs tied to users

## Notes

Performance is critical since digest queries will happen at scale. Ensure indexes are properly planned.
