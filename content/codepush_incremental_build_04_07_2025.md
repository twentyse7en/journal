# CodePush Incremental Update System
Note: This is summary of conversation I had with cursor.

## Overview
CodePush's incremental update system is a smart mechanism that allows mobile applications to download only the files that have changed between versions, rather than downloading entire app packages. This dramatically reduces bandwidth usage, improves update speed, and enhances user experience.
## How It Works
### File-Level Tracking
- Each package maintains a manifest containing SHA-256 hashes for every file
- Files are tracked individually with normalized paths
- System files like .DS_Store and __MACOSX folders are automatically excluded
### Diff Generation Process
1. **Comparison Phase**: New package manifest is compared against previous version
2. **Change Detection**: System identifies added, modified, and deleted files
3. **Diff Creation**: Creates a minimal update package containing only changes
4. **Metadata Generation**: Produces instructions for client-side application
### Update Package Structure
A diff package contains:  
- **Changed files only** - Modified or new files from the update
- **Deletion manifest** - List of files to remove from previous version
- **Update metadata** - Instructions for applying the incremental update
### Client Update Decision Flow
When a client requests an update, the server:
1. Identifies client version using package hash or label
2. Checks for available diffs from client's current version
3. Chooses optimal delivery method:
    - Incremental update if diff is available and smaller
    - Full package download as fallback
## Benefits
### Performance Advantages
- Faster Downloads: Updates can be 40-90% smaller than full packages
- Reduced Bandwidth: Especially beneficial for mobile users with data limitations
- Quicker Application: Less download time means faster user experience
- Background Updates: Smaller packages enable seamless background updating
### Cost Optimization
- Lower CDN Costs: Reduced bandwidth translates to lower hosting costs
- Storage Efficiency: Diff packages require minimal additional storage
- Scalability: Benefits multiply with larger user bases
### User Experience
- Minimal Interruption: Updates happen faster with less user waiting
- Data-Friendly: Important for users on limited data plans
- Transparent Operation: Works automatically without user intervention
## Real-World Impact
### Example Scenario
- Traditional Update: 5 MB app requires full 5 MB download for small bug fix
- Incremental Update: Same fix might only require 500 KB download
- Result: 90% bandwidth savings and 10x faster update
### Use Cases
- Bug fixes: Small code changes result in minimal downloads
- Feature updates: Only new functionality needs to be downloaded
- Asset updates: Image or resource changes don't require full app download
## System Architecture
### Storage Components
- Full Package Blob: Complete application package for new installations
- Manifest Blob: JSON file containing file hash mappings
- Diff Package Map: Index of available incremental updates from various versions
### Decision Logic
The system automatically chooses between:
- Incremental delivery when client version is known and diff exists
- Full package delivery for new users or when diffs aren't available
## When Incremental Updates Are Available
### Prerequisites
- Client must have a known previous version
- Manifest files must exist for both old and new versions
- Diff generation must have completed successfully
- Incremental package must be smaller than full package
### Fallback Scenarios
- First-time app installations
- Missing manifest files (single-file updates)
- Failed diff generation
- Cases where incremental update would be larger than full download
## Technical Considerations
### Manifest Blob Importance
- Critical for diff generation: Without manifests, only full downloads possible
- Storage trade-off: Deleting old manifests saves space but eliminates incremental updates
- Backward compatibility: System gracefully falls back to full downloads
### Optimization Strategy
- Keep recent manifests: Maintain manifests for actively used versions
- Clean old manifests: Remove manifests for versions with minimal usage
- Monitor usage patterns: Base retention decisions on actual user distribution
## Key Advantages
- Automatic Intelligence: Server automatically selects optimal update method
- Backward Compatibility: Always maintains full download capability
- Developer Transparency: Works without requiring code changes
- Scalable Architecture: Benefits increase with user base size
- Cost Effective: Reduces infrastructure costs while improving performance
## Summary
The incremental update system transforms mobile app updating from a heavyweight operation into a lightweight, efficient process. By tracking changes at the file level and delivering only modifications, it provides significant benefits in terms of speed, cost, and user experience while maintaining full backward compatibility and automatic fallback capabilities.
This system is essentially "version control for mobile app updates" - intelligently managing and delivering only the differences between versions to create an optimal update experience.