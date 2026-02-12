# OpenSpace.Content - Repository Rules

## Overview
This repository contains all YAML-based content definitions, schemas, and data for the OpenSpace game.

## Content Organization

### Core Content Areas
- **Ages**: Game eras and technological progression
- **Atmospheres**: Planet atmospheric definitions
- **Authorities**: Government and political systems
- **Ethics**: Faction moral and behavioral frameworks
- **Events**: Game events and narrative triggers
- **Resources**: Economic resources and materials
- **Species**: Alien species definitions and traits
- **Units**: Military units and combat entities

### Assets and Media
- **GFX**: Graphics, UI assets, and visual content
- **Models**: 3D models in GLTF format
- **Textures**: Texture assets and materials
- **Localization**: Text localization files

## Key Enforcements

### Schema-First Approach
- **Define schemas before creating content**
- Cursor can generate YAML schemas for new content types
- Maintain schema validation and warn on violations

### Content Validation
- **Warn on invalid YAML** against defined schemas
- **Cross-reference validation** for content dependencies
- **Version compatibility** with Unity and other repos

### File Organization
- **Schema and data coexistence** allowed in same directories
- **Consistent naming conventions** across content types
- **Clear separation** between different content domains

### Unity Integration
- **No code in Content** - pure data definitions only
- **Contract-based integration** with Unity systems
- **Version synchronization** with Unity builds

## Development Workflow

### Content Creation Process
1. Define YAML schema for new content type
2. Create content data files following schema
3. Validate against schema definitions
4. Test integration with Unity systems
5. Update documentation as needed

### Schema Generation
- Request Cursor to generate schemas for new content types
- Follow established patterns from existing schemas
- Include validation rules and cross-references

## Related Repositories
- **OpenSpace.Unity**: Consumes content through contracts
- **OpenSpace.Build**: Includes content in builds
- **OpenSpace.Launcher**: Validates content versions
- **OpenSpace.Docs**: Documents content schemas and usage

---

*For complete project rules, see: [`/cursor-rules.md`](../../cursor-rules.md)*
