# Healthcare Provider Quality Metrics Data Generator

A Python tool for generating realistic provider-level healthcare quality metrics data. Creates weekly performance data across multiple quality measures for healthcare providers, complete with benchmarks, trends, and performance variations.

## 🎯 Overview

This generator creates comprehensive healthcare quality data for:
- **Provider Performance Tracking**: Weekly quality metrics for individual providers
- **Quality Improvement**: Benchmark comparisons and trend analysis
- **Healthcare Analytics**: Testing dashboards and quality reporting systems
- **Performance Management**: Identifying high and low-performing providers

## 🏥 Features

### 📊 Quality Measures
- **Clinical Quality**: Hypertension control, diabetes management, depression screening
- **Preventive Care**: Cancer screenings, immunization status
- **Patient Experience**: Patient satisfaction scores
- **Care Coordination**: Referral turnaround times, medication adherence
- **Population Health**: Statin therapy for CVD prevention

### 🎭 Realistic Performance Patterns
- **Provider Variation**: Different performance levels across providers
- **Temporal Trends**: Improving, declining, or stable performance over time
- **Benchmark Alignment**: Industry-standard targets for each measure
- **Low Performers**: Designated underperforming providers for analysis

### 📈 Data Characteristics
- **52 weeks** of historical data per provider
- **12 quality measures** across clinical domains
- **Consistent patient panels** with minimal weekly variation (±2%)
- **Realistic numerator/denominator** relationships

## 📁 Output Data

### File: `provider_kpi_data.csv`
**Total Records**: ~15,000 (24 providers × 12 measures × 52 weeks)

| Field | Type | Description |
|-------|------|-------------|
| Clinic | String | Clinic name (North, South, East, West) |
| Provider | String | Provider name (Dr. First Last) |
| MeasureName | String | Quality measure name |
| MeasureDate | Date | Week ending date (YYYY-MM-DD) |
| LowerIsBetter | Integer | 0=higher better, 1=lower better |
| Numerator | Integer | Patients meeting quality criteria |
| Denominator | Integer | Total eligible patients |
| MeasureValue | Decimal | Performance rate (numerator/denominator) |
| Benchmark | Decimal | Industry standard target |

## 🛠️ Installation

### Requirements
```bash
pip install pandas numpy
```

### For Databricks
```python
%pip install pandas
```

## 💻 Usage

### Basic Usage
```python
import pandas as pd
from quality_metrics_generator import generate_provider_kpi_data

# Generate quality metrics data
df_kpi = generate_provider_kpi_data(measures, benchmarks, trends, clinic_providers)

# Save to CSV
df_kpi.to_csv("provider_kpi_data.csv", index=False)
```

### Customization
```python
# Add custom measures
custom_measures = {
    'Blood Pressure Screening': 0,  # Higher is better
    'Hospital Readmissions': 1      # Lower is better
}

# Set custom benchmarks
custom_benchmarks = {
    'Blood Pressure Screening': 0.90,
    'Hospital Readmissions': 0.08
}

# Define trends
custom_trends = {
    'Blood Pressure Screening': 'up',
    'Hospital Readmissions': 'down'
}
```

## 📊 Quality Measures Included

### Clinical Quality (Higher is Better)
| Measure | Benchmark | Trend | Description |
|---------|-----------|-------|-------------|
| Hypertension Control | 72% | ↑ | Patients with controlled blood pressure |
| Diabetes Control | 50% | ↑ | Diabetic patients with HbA1c < 8% |
| Depression Screening | 75% | ↑ | Annual depression screening rate |
| Depression Remission | 60% | ↑ | Patients achieving remission |
| Medication Adherence | 80% | → | PDC ≥ 0.8 for chronic medications |

### Preventive Care (Higher is Better)
| Measure | Benchmark | Trend | Description |
|---------|-----------|-------|-------------|
| Breast Cancer Screening | 82% | ↑ | Mammography screening rate |
| Colon Cancer Screening | 80% | ↑ | Colonoscopy/FIT screening rate |
| Adult Immunization Status | 70% | ↑ | Up-to-date vaccination rate |
| Statin Therapy for CVD Prevention | 75% | ↑ | Appropriate statin prescribing |

### Patient Experience & Operations (Mixed)
| Measure | Benchmark | Trend | Direction |
|---------|-----------|-------|-----------|
| Patient Satisfaction | 85% | ↑ | Higher is Better |
| Senior Readmissions | 12% | ↓ | Lower is Better |
| Referral Turnaround Time | 90% | ↓ | Lower is Better |

## 🏢 Provider Network Structure

### 4 Clinics, 24 Total Providers
- **North Clinic**: 6 providers (includes Dr. Michael Chen - low performer)
- **South Clinic**: 6 providers (includes Dr. Rachel Martinez - low performer)
- **East Clinic**: 6 providers (slightly below average performance)
- **West Clinic**: 6 providers (average performance)

### Performance Patterns
- **Low Performers**: 15% below benchmark, declining trends
- **East Clinic**: 5% below average across all providers
- **Other Providers**: Normal performance around benchmarks

## 📈 Sample Analytics

### Provider Performance Analysis
```python
# Calculate provider rankings
provider_summary = df_kpi.groupby(['Clinic', 'Provider', 'MeasureName']).agg({
    'MeasureValue': 'mean',
    'Benchmark': 'first'
}).reset_index()

provider_summary['Performance_vs_Benchmark'] = (
    provider_summary['MeasureValue'] / provider_summary['Benchmark']
)

# Identify top and bottom performers
top_performers = provider_summary.nlargest(10, 'Performance_vs_Benchmark')
bottom_performers = provider_summary.nsmallest(10, 'Performance_vs_Benchmark')
```

### Trend Analysis
```python
# Analyze performance trends over time
df_kpi['MeasureDate'] = pd.to_datetime(df_kpi['MeasureDate'])
monthly_trends = df_kpi.groupby(['Provider', 'MeasureName', 
                                pd.Grouper(key='MeasureDate', freq='M')])['MeasureValue'].mean()

# Calculate improvement rates
improvement_analysis = monthly_trends.groupby(['Provider', 'MeasureName']).apply(
    lambda x: (x.iloc[-1] - x.iloc[0]) / x.iloc[0] * 100
)
```

### Quality Scorecards
```python
# Create provider scorecards
scorecard = df_kpi.groupby(['Clinic', 'Provider']).apply(lambda x: {
    'measures_above_benchmark': (x['MeasureValue'] > x['Benchmark']).sum(),
    'total_measures': len(x['MeasureName'].unique()),
    'average_performance': x['MeasureValue'].mean(),
    'benchmark_achievement_rate': (x['MeasureValue'] > x['Benchmark']).mean()
})
```

## 🎯 Use Cases

### Quality Improvement
- **Provider Coaching**: Identify areas for improvement
- **Best Practice Sharing**: Learn from top performers
- **Trend Monitoring**: Track quality improvements over time
- **Benchmark Analysis**: Compare against industry standards

### Healthcare Analytics
- **Dashboard Testing**: Populate quality dashboards with realistic data
- **Reporting Systems**: Test automated quality reports
- **Data Pipelines**: Validate ETL processes for quality data
- **Machine Learning**: Train models for quality prediction

### Performance Management
- **Provider Reviews**: Support performance evaluations
- **Incentive Programs**: Model pay-for-performance systems
- **Quality Contracts**: Analyze quality-based contracts
- **Regulatory Reporting**: Test compliance reporting systems

## 🔧 Configuration Options

### Scaling
- **More Providers**: Add providers to existing clinics
- **More Clinics**: Expand the clinic network
- **Time Period**: Adjust `weeks_back` parameter (default: 52)
- **Measures**: Add/remove quality measures

### Performance Tuning
- **Variation Levels**: Adjust random noise (default: ±2.5%)
- **Trend Strength**: Modify drift rates (default: ±0.5% per week)
- **Benchmark Alignment**: Set starting performance relative to benchmarks

### Custom Scenarios
```python
# Create custom performance scenarios
custom_scenarios = {
    'high_performer': {'modifier': 1.1, 'trend': 0.008},
    'improving': {'modifier': 0.9, 'trend': 0.010},
    'declining': {'modifier': 1.0, 'trend': -0.008}
}
```

## 📊 Data Quality Features

### Realistic Patterns
- **Consistent Panels**: Patient denominators vary minimally week-to-week
- **Logical Relationships**: Performance aligns with provider characteristics
- **Seasonal Variation**: Natural fluctuations in quality metrics
- **Benchmark Correlation**: Performance generally clusters around benchmarks

### Statistical Properties
- **Normal Distribution**: Performance follows realistic statistical patterns
- **Autocorrelation**: Weekly values show appropriate time-series correlation
- **Cross-Measure Correlation**: Related measures show expected relationships
- **Outlier Management**: Extreme values are bounded within realistic ranges

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-measure`)
3. Commit changes (`git commit -am 'Add new quality measure'`)
4. Push to branch (`git push origin feature/new-measure`)
5. Create a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👤 Author

**Jason Votaw** - *Healthcare Data Architect*
- Expertise in healthcare quality metrics and provider performance analysis
- Focus on realistic healthcare data generation for analytics and testing

## 🆘 Support

For questions or issues:
1. Check existing [Issues](../../issues)
2. Create a new issue with detailed description
3. Include sample output and configuration details

## 🏷️ Version History

- **v1.0.0** - Initial release with 12 quality measures
- **v1.1.0** - Added low-performing provider scenarios
- **v1.2.0** - Enhanced trend modeling and benchmark alignment

## 🔗 Related Projects

- [Healthcare Financial Data Generator](../financial-generator) - Capitation and claims data
- [MMR Flat File Parser](../mmr-parser) - Medicare payment file processing
- [Claims Data Generator](../claims-generator) - Healthcare claims simulation

---

*Designed for healthcare quality professionals, data analysts, and healthcare technology developers working with provider performance data.*
